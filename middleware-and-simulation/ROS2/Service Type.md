Similar to [[Topic Type]], Service Type describes how to request and response data of a [[Services]] should be structured. Unlike [[Topic Type]], Service Type has two parts - one for [[Service Request]] and one for [[Service Response]]. 

To view the current running services and the service types:
`ros2 service list -t`.
This prints:
`/node_name/service_name [service_type]`
To inspect the structure of a service type:
`ros2 interface show [service type]`
Which returns
``` 
[Request Structure]
---
[Response Structure]
```
This is defined in the service message file `.srv`. 

To locate the actual file, 
`ros2 pkg prefix <package_name>`. This will print the installation path, then navigate to
`<install_path>/share/<package_name>/srv/`.

====📝 If the package is installed via binaries (e.g. `apt`), the source `.srv` file might not be present. You may need to download the source from the ROS 2 GitHub repository if you want to inspect or modify it.====

If you **modify** a `.srv` file, you **must rebuild** the package so that `rosidl` can regenerate the C++/Python interface code. If more than one package uses this service type definitions, all of the packages that share this service type must be rebuilt.
`colcon build --packages-up-to <your_package>`

If you **create** a **custom service type**, it must be registered in the package's [[CMakeLists]].txt so that the build system `rosidl` knows how to generate appropriate C++/Python interface code.

``` CMakeLists.txt
rosidl_generate_interfaces(${PROJECT_NAME} 
	"srv/AddTwoInts.srv" 
	DEPENDENCIES std_msgs              // IF dependent on standard message types
	)

//Declare that this package exports this dependency. For other packages
ament_export_dependencies(rosidl_default_runtime)

```
Then update [[package.xml]]
``` 

<build_depend>rosidl_default_generators</build_depend> <exec_depend>rosidl_default_runtime</exec_depend> <build_depend>builtin_interfaces</build_depend> <exec_depend>builtin_interfaces</exec_depend>

```
