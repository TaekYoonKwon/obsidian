When the server starts, Gazebo GUI requests the scene information from the server. This essentially queries - what am I trying to render, where can I find information about them.
This is conducted as a [[Gazebo Service]]. Similar to ROS2 service, a synchronous communication between processes. 

Using gazebo cli, we can probe what the server is trying to get the GUI to render.
This is useful when you are trying to render a scene that is detached from the GUI: you have gz sim -s (headless server) running somewhere, and you are trying to connect your local gz sim -g (GZ GUI only). 

1. See whether the service is available, by listing `gz service -l`.  If `world/{world}/scene/info` is not available, the server may have a different `GZ_PARTITION`.
2. Once service is available, we can query what that topic is about. 
   ```
connor@connor-syos:~/syos-repo/syos-ardupilot-gazebo$ gz service -i -s /world/harby_runway_with_car/scene/info  
Service providers [Address, Request Message Type, Response Message Type]:  
 tcp://172.17.0.1:34757, gz.msgs.Empty, gz.msgs.Scene
   ```
3. Make a empty request to `scene/info` service, then the server will return with everything the GUI needs to know in order to render the scene. 
```
gz service -s /world/harby_runway_with_car/scene/info --reqtype gz.msgs.Empty --reptype gz.msgs.Scene --timeout 5000 --req ''
```

### Caveat: absolute path baking 
The scene response contains fully resolved absolute paths for all mesh and texture URIs. Even if your model SDFs use `model://` URIs, the [[SceneBroadcaster]] system resolves them against the server's `GZ_SIM_RESOURCE_PATH` before publishing. This means the GUI receives paths like `/sim/run/models/harby/meshes/rud_actuator.stl` - paths that only exist inside the container.

If the GUI is running on a different filesystem (e.g. host machine connecting to a containerized server), it cannot find these files. The workaround is to symlink the container paths on the host:
```
sudo mkdir -p /sim/run
sudo ln -s ~/syos-repo/syos-ardupilot-gazebo/models /sim/run/models
```

This is a limitation of Gazebo's architecture - the server does not stream file contents to the GUI, it only sends resolved paths and expects the GUI to load them locally.