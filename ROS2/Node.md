Each node in ROS is responsible for a single, modular task. The principle with ROS to achieve a fully functional robot is *divide and conquer* — break down the robot’s functionality into small, focused components.

With each component in the system defined as a discrete task and a node, we can build a **network of nodes** which communicate and coordinate with each other to achieve the functional requirements. This network forms what’s known as the [ROS graph].

Nodes:
- run independently and communicate with each other via:
	- [[Topics]]
	- [[Services]]
	- [[Actions]]
- can be reused across different packages if modular
- can have [[Node Parameters]] (configurable settings)
- can be remapped at **runtime** using --ros-args
- can be grouped into [[Composable Nodes]] that runs on a same process for efficiency
- are **executables**

To get more information on a particular node:
``` 
// To view the list of currently active nodes
ros2 node list
// Get the info 
ros2 node info /[node_name]
```
This will print all [[Subscribers]], [[Publishers]], [[Service Servers]], [[Service Clients]], [[Action Servers]] and [[Action Clients]] associated with this node. 