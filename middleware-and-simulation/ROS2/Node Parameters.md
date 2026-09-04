A _Node Parameter_ is a runtime-configurable setting associated with a node. It allows changing node behavior without modifying source code.

They can be of types such as integers, booleans, strings, floats and lists. They are initialised, associated with individual nodes. 

They are used to configure nodes at startup without changing the code. The lifetime of a parameter is confined to during the lifetime of the associated [[node]]. 

Each parameter consists of - 
	- Key: `string`
	- Value: One of `bool`, `int64`, `float64`, `string`, `byte[]`, `bool[]`, `int64[]`, `float64[]` or `string[]`
	- Description: contain parameter descriptions, value ranges, type information and additional constraints.

By default, parameters must be **explicitly declared** before use, typically at startup. This ensures **deterministic initialization**. You can declare them manually or load from a YAML file. Changing the ***type*** of a declared parameter at runtime *will fail*. However, to make it dynamic, refer to `ParameterDescriptor - dynamic_typing`.

====Note: If not all parameters are known ahead of time, the node can be instantiated with `allow_undeclared_parameters` set to `true`. ====

When parameters are changed, they invoke [[Parameter Callback]] registered to the node. 

## Interaction with Parameters - via Services
- `/node_name/describe_parameters`
	- [[Service type]] of `rcl_interfaces/srv/DescribeParameters`. 
	- Given a list of parameter names, returns a list of descriptors associated with the parameters.
- `/node_name/get_parameter_types`
	- [[Service type]] of `rcl_interfaces/srv/GetParameterTypes`
	- Given a list of parameter names, returns a list of parameter types associated with the parameters.
- `/node_name/get_parameters`
	- [[Service type]] of `rcl_interfaces/srv/GetParameters`
	- Given a list of parameter names, returns a list of parameter values associated with the parameters.
- `/node_name/list_parameters`
	- [[Service type]] of `rcl_interfaces/srv/ListParameters`
	- Given an optional list of parameter prefixes, returns a list of the available parameters with that prefix. If the prefixes are empty, returns all parameters.
- `/node_name/set_parameters`
	- Service type of `rcl_interfaces/srv/SetParameters`
	- Given a list of parameter names and values, attempts to set the parameters on the node. Returns a list of results from trying to set each parameter; some of them may have succeeded and some may have failed.
- `/node_name/set_parameters_atomically`
	- Service type of `rcl_interfaces/srv/SetParametersAtomically`
	- Given a list of parameter names and values, attempts to set the parameters on the node. 
	- Returns a single result from trying to set all parameters, so if one failed, all of them failed.

## Interaction with Parameters - via CLI
- `ros2 param list`
	- Lists all parameters belonging to the current nodes.
- `ros2 param get <node_name> <node_param_name>`
	- Display the type and value of a parameter.
- `ros2 param set <node_name> <node_param_name> <args>`
	- Change a parameter value at runtime.
- `ros2 param dump <node_name> | > output.yaml`
	- Prints to the `stdout` (by default), all of the node's parameter values. This can be redirected to a file to save the parameter values configuration.
- `ros2 param load <node_name> <parameter_file>`
	- To load the parameters saved from the previous command.
- `ros2 run <package_name> <executable_name> --ros-args --params-file <file_name>`
	- Start the node with the saved parameter values. 
	- e.g `ros2 run turtlesim turtlesim_node --ros-args --params-file turtlesim.yaml`

Through YAML files or individual command-line arguments, the parameters can be initialised.

