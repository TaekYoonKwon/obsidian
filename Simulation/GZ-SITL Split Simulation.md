The objective of this task is to set up a containerised instance of SITL and the gazebo world, and have them communicate via their own docker bridge network.
This was deemed theoretically possible, since [[Gazebo Messages]] and [[Gazebo Transport]] respectively takes care of the serialisation and transport. 

## 1. First experiment with host network
First experiment was to setup the containers - vehicle container which has SITL instance and the world container which has the gazebo world running inside it. With network=host, Theoretically this should be no different to running it outside the container. 

Upon starting the world container, the container should expose its UDP port for the SITL to connect to, which according to our model SDF, was **127.0.0.1:9002**.
```
<plugin name="ArduPilotPlugin"

filename="ArduPilotPlugin">

<!-- Port settings -->

<fdm_addr>127.0.0.1</fdm_addr>

<fdm_port_in>9002</fdm_port_in>
```

Indeed, after we spin the container, and run the command `sudo ss -tulpn`, we confirm the world container has the UDP port open: 
```
udp       UNCONN      0           0                    127.0.0.1:9002                 0.0.0.0:*         users:(("ruby",pid=164691,fd=32))
```

And upon starting SITL,  