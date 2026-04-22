##The commands and the meanings
- `ros2 run [package_name][node_name][user-defined-args] --ros-args --remap __node:=[alternative_node_name] `
	- Package is a container for nodes, [[shared resources]] definitions and so on. This must be defined by [[package.xml]]. Each package has a [[CMakeLists]].txt for C++ and `setup.py` for Python.
	- [[Node]] 
	- An analogy would be a package is a recipe for a dish (pasta) and node is each entity that makes up the recipe - oil, pasta, water, stovetop etc.
	- Here `--ros-args --remap __node:= [alternative_node_name`tells ROS to remap the internal name of this node to something else.
	- This allows running multiple instances of the same node and be able to differentiate them.
- `rqt`
	- Provides a GUI to interact and visualise various aspects of a ROS system.
	- ![[Pasted image 20250416215824.png]]
	- For now, `turtlesim` used `rqt` to select services and add parameters, then press `call`, to create a [[Service Request]] to the node turtlesim.
	- Can also open `rqt_graph` by selecting Plugins -> Introspection -> Node Graph
- `rqt_graph`
	- ![[Pasted image 20250416221345.png]]
	- Can hover over [[Topics]] (the boxes) to see the [[Subscribers]] and [[Publishers]] associated with the topic. 
- `ros2 topic list -t`
	- Returns the list of [[Topics]] active in the current session. 
	- `-t` will return the associated [[Topic Type]]
	- This shows what each type of message each topic is expecting.
	- This type will defines a specific structure, which both the publisher and subscriber are expected to adhere to.
	- For nodes to interact with a topic, both the topic name and message type has to agree.
- `ros2 topic echo <topic name>`
	- We can use echo to introspect the topic and "sniff" on the topic
	- This works by creating another node that subscribes to that topic. 
- `ros2 topic info <topic name>`
	- This shows the topic type and the number of subscribers and publishers on the topic,
- `ros2 interface show <topic type>`
	- This prints specifically what structure of data the message expects for the topic.
	- FYI - all topic types resemble the naming `<package_name>/msg/<msg_name>`
- `ros2 topic pub <topic_name> <msg_type> <args>`
	- This can be used to inject and publish a message directly to a topic.
	- Without any extra options attached, this will publish the message defined by `<args>` every second. 
	- To publish only once, add `--once` option
	- To ensure subscribers are ready to receive the message being published, use `-w <num>` where num is the number of subscribers that **match** the topic name and topic type (ie. valid subscribers). 
	- ====\<note> if you are using `ros2 topic echo`, that creates a temporary node as well, and will count towards the number of matching subscribers.====	
	- E.g) To format `args`: 
		linear:
		   x: 2.0
		   y: 0.0
		   z: 0.0
		angular:
		  x: 0.0
		  y: 0.0
		  z: 0.0
		- "{linear: {x: 2.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 1.8}}"
- `ros2 service list -t` 
	- Lists all the active services and their service types.
- `ros2 service call <service_name> <service_type> <args>`
	- Given we know what the [[service]] name, what service type it is associated with, we can call a service and create a [[service request]]. 
	- E.g) To format args:
	  `ros2 service call /spawn turtlesim/srv/Spawn "{x: 2, y: 2, theta: 0.2, name: ''}"`
- `ros2 node info <node name>`
	- This prints all topic subscribers and publishers, service/[[action]] client and servers registered in the node.
- `ros2 action list -t`
	- Lists all active actions and the action types.
- `ros2 bag`
	- `ros2 bag record -o <bag_name> <topic1_name> <topic2_name> ... `
	- `ros2 bag record --service <service_name>`
	- Capture all data published on topics and services. Useful for debugging topic and service interaction.
	- ====If not necessary to save to a file and you just want to view the ongoing traffic, sufficient to use `ros2 [service][topic] echo [service_name][topic_name]`====
- Steps to create a new package
	- In the `src` folder in the workspace:
		`ros2 pkg create --build-type ament_cmake --license Apache-2.0 <package_name> --dependencies rclcpp example_interfaces`
		- This will create a barebone C++ project with inc, src folders and [[CMakeLists]] and [[package.xml]].
	- Create source files and header files as required.
	- Update [[CMakeLists]] and [[package.xml]] files to indicate the dependencies and requirements. Namely:
		- To CMakeLists.txt:
			- `add_executable([node_name] [source_file_dir])`
			- `ament_target_dependencies([node_name] [dependency ... ]])`
			- `install(TARGETS [node_name] DESTINATION lib/${PROJECT_NAME})`
	- Build the package from the **workspace directory**
		`colcon build --packages-select <package_name>`
	- Source the overlay
		`source install/setup.bash`
	- Run the nodes defined in the package
		- `ros2 run <package_name> <node_name>`
- Steps to create a new service:
	- Inside the source file for the server, inside the `main` function:
		- `rclcpp::init(argc, argv);`
			Initialises the ROS 2 C++ library
		- `std::shared_ptr<rclcpp::Node> node = rclcpp::Node::make_shared("add_two_ints_server");`
			Create the server node.
		- `rclcpp::Service<[srv]>::SharedPtr service = node->create_service<[srv]>("[service_name]", &[helper_func]);`
			Create the service, pass the helper function address as the parameter.
		- `rclcpp::spin(node);`
			Spin the node, make the service available. 
	- Inside the source file for the client, inside the `main` function:
		- `std::shared_ptr<rclcpp::Node> node = rclcpp::Node::make_shared([client_node_name]);`
			Create the client node
		- `rclcpp::Client<[srv]>::SharedPtr client = node->create_client<[srv]>([service_name]);`
			Create the client instance of the client node
		- Wait until the client becomes available. 
			```
			while (!client->wait_for_service(1s)) {
				if (!rclcpp::ok()) {
					RCLCPP_ERROR(rclcpp::get_logger("rclcpp"), "Interrupted while waiting for the service. Exiting.");

					return 0;
				}
				RCLCPP_INFO(rclcpp::get_logger("rclcpp"), "service not available, waiting again...");
			}
			```
		- `auto request = std::make_shared<[srv]>();`
			At this point, the request is created, and the request data may be populated.
		- `auto result = client->async_send_request(request);`
			Send the request to the client. 