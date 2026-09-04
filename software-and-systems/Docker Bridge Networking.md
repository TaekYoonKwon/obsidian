In trying to containerise the simulation for world (Gazebo server session) and the vehicle ([[ArduPilot SITL]]) and isolating them from the rest of the network, via default docker bridge for a compose, there were issues with ArduPilot SITL could not communicate FDM with Gazebo Server. 
Using `network_mode: host`, these two containers were able to establish communication, so the suspicion was with how we specify the FDM input address. The suggestion from the Chatbot was that we must use --sim-address=world. 

# Docker Bridge
To allow containers to communicate over a network interface and implement some kind of traffic control among them, Docker uses Bridge, which are like software abstraction of physical network.  Just like real networks, bridges provide separation.  
## Custom Network Configuration
By default, manually started containers without network tag, all attach to docker bridge network named bridge. This default network is `bridge`, but this default bridge is limited in terms of its capability compared to the custom bridge.

**When performing docker compose, in absence of network_mode for any of the services, docker daemon will automatically create a custom bridge. The default is only used if you do *docker run image cmd*..**

The main advantages of custom bridges are:
1. Automatic DNS resolution between containers on the same bridge:
   For example, if you have a docker container `world` and `vehicle` in the same default bridge, unless you use `--link` between every containers in both directions, which gets confusing fast. But if the same containers were on custom bridge, they would be able to ping each other like `world:14550` etc. 
2. Better isolation
   Because anyone can join the default bridge, if you have a custom bridge, only the containers on the bridge can communicate with each other, which is the same principle but with limited access to the bridge.
3. Flexibility
   Containers initialised with custom bridges are able to connect/disconnect to different custom bridges. Containers with default bridges cannot do that - the containers need to be killed then restarted with the correct networking option.
4. Configurability
   Default bridge uses the same settings such as MTU and iptables rules, and changing this rule will require restarting of the docker daemon. 

### The Culprit with Communication Failure
From inside the `vehicle` container, We were able to send packets to `world` container, so there was no problem with the network configuration. The problem was, naively passing in the arguments for sim-address as the hostname - the ArduPilot launch script cannot parse this natively. 

Inside `libraries/AP_Networking:466 const char * address_to_str`, it calls `inet_addr`, which requires the input to be in a dotted-decimal IPv4 string (e.g, 192.168.1.1). So the solution was to resolve this hostname manually using an inline python script,  
```yaml
command: >-

bash -c "sleep 5 && 
SIM_ADDR=$(python3 -c \"import socket; print(socket.gethostbyname('world'))\") &&
MISSION_ADDR=$(python3 -c \"import socket; print(socket.gethostbyname('mission'))\") &&
sim_vehicle.py -v ArduPlane -f gazebo-harby --model JSON --out udp:$$MISSION_ADDR:14551 --sim-address=$$SIM_ADDR --speedup 1"
```

