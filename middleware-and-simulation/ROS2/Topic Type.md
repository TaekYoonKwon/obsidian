Topic Types define the message types that nodes must adhere to in order to communicate with one another over [[Topics]].  

These topic types are created with `.msg` files. The `.msg` files are readable and simple to follow. It is one of the [[Interface]] for ROS2.
``` 
  
# The time stamp when this parameter event occurred.  
builtin_interfaces/Time stamp  
       int32 sec  
       uint32 nanosec  
  
# Fully qualified ROS path to node.  
string node  
  
# New parameters that have been set for this node.  
Parameter[] new_parameters  
       string name  
       ParameterValue value  
               uint8 type  
               bool bool_value  
               int64 integer_value  
               float64 double_value  
               string string_value  
               byte[] byte_array_value  
               bool[] bool_array_value  
               int64[] integer_array_value  
               float64[] double_array_value  
               string[] string_array_value  
  
# Parameters that have been changed during this event.  
Parameter[] changed_parameters  
       string name  
       ParameterValue value  
               uint8 type  
               bool bool_value  
               int64 integer_value  
               float64 double_value  
               string string_value  
               byte[] byte_array_value  
               bool[] bool_array_value  
               int64[] integer_array_value  
               float64[] double_array_value  
               string[] string_array_value  
  
```

- The field types definitions can be found here: https://design.ros2.org/articles/interface_definition.html#field-types
- The indentation represents nesting, implemented as a `struct` in C++.
	- `stamp` is a struct which holds `sec` and `nanosec` as its members.
- It is acceptable to define the same name if it is nested.
	- When you build the [[package]], this `.msg` file is compiled by the `rosidl` code generators, turned into an [[IDL file]]. 
	- This will define every field types and definitions on the `.msg` file to C++ struct/class.
- \# to comment

In order to define a custom `.msg` file for a topic and use it, the [[CMakeLists]] - which holds the recipe for the package - must be aware of this new topic type, so that it can be compiled and be referenced in the package. 

``` 
rosidl_generate_interfaces(${PROJECT_NAME} // Generate the IDL file
  "<directory>/<custom_msg>.msg"
)

// declare that the package exports this dependency
ament_export_dependencies(rosidl_default_runtime) 
 
```

*ament_export_dependencies ensures any other package that has the package as its dependency will also include this topic type, so that it can subscribe/publish to the topic if it wants*

Then update [[package.xml]]
``` 

<build_depend>rosidl_default_generators</build_depend> <exec_depend>rosidl_default_runtime</exec_depend> <build_depend>builtin_interfaces</build_depend> <exec_depend>builtin_interfaces</exec_depend>

```
