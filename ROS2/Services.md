Services are similar to [[Topics]] in a sense that they both facilitate communication between two nodes. The difference comes in the model of communication. 

Services are based on a call-and-response model. This means the data is provided when they are specifically called by a client. The provided data adheres to the format specified by [[Service Type]], similar to [[Topic Type]].

Services are most suitable for actions like:
- Intialisation of a component
- Querying data on-demand
- Execute atomic operations (such as reset and spawn)
====Note generally Services are not suitable for continuous, high-rate calls. ====

Service calls are ***blocking*** by default. The client will wait until it gets a response or times out. If the client cannot afford to wait in case of server missing, timeouts and exceptions must be handled in the code.  

For a service, there is only one [[Service Servers]], but there can be as many as [[Service Clients]].

To view current services, type `ros2 service list -t`. The results show 
`<<node name>>/<<service name>> [service type] `. Most nodes in ROS2 have the following 6 infrastructure services that [[Parameters]] are built off. 
``` 
/turtlesim/describe_parameters
/turtlesim/get_parameter_types
/turtlesim/get_parameters
/turtlesim/list_parameters
/turtlesim/set_parameters
/turtlesim/set_parameters_atomically

```
It is possible to interact directly with a service. By using `ros2 service call <service_name> <service_type> <arguments>`, the shell creates a temporary node which makes that [[service request]]. 
====Note service call in command line lets you partially get away with missing parameters. If the types or arguments provided in `ros2 service call` are wrong, it doesn’t always throw a hard error — the CLI does its best to fill defaults but will fail silently or print errors depending on the type mismatch severity.====

In order to view the service communication between a [[Service Clients]] and a [[Service Servers]], **introspection** must be explicitly turned on. This is because unlike [[topics]] which persist, service calls are transient and synchronous. So without introspection enabled, there is no built-in recording. **Introspection** enables visibility into the internal DDS middleware traffic related to services.
```
ros2 param set <service_name> service_configure_introspection contents
ros2 param set <client_name> client_configure_introspection contents
ros2 service echo <service_name | service_type> <arguments>

info:
  event_type: REQUEST_SENT
  stamp:
    sec: 1709408301
    nanosec: 423227292
  client_gid: [1, 15, 0, 18, 250, 205, 12, 100, 0, 0, 0, 0, 0, 0, 21, 3]
  sequence_number: 618
request: [{a: 2, b: 3}]
response: []
---
info:
  event_type: REQUEST_RECEIVED
  stamp:
    sec: 1709408301
    nanosec: 423601471
  client_gid: [1, 15, 0, 18, 250, 205, 12, 100, 0, 0, 0, 0, 0, 0, 20, 4]
  sequence_number: 618
request: [{a: 2, b: 3}]
response: []
---
info:
  event_type: RESPONSE_SENT
  stamp:
    sec: 1709408301
    nanosec: 423900744
  client_gid: [1, 15, 0, 18, 250, 205, 12, 100, 0, 0, 0, 0, 0, 0, 20, 4]
  sequence_number: 618
request: []
response: [{sum: 5}]
---
info:
  event_type: RESPONSE_RECEIVED
  stamp:
    sec: 1709408301
    nanosec: 424153133
  client_gid: [1, 15, 0, 18, 250, 205, 12, 100, 0, 0, 0, 0, 0, 0, 21, 3]
  sequence_number: 618
request: []
response: [{sum: 5}]
---
```
Here the *client_gid* indicates which client created this request. However, it isn't straightforward to backtrack this. Easiest way is to log the client GID when creating a service client so it can be referred later, or make the clients identifiable so that timing and behaviour can be inferred via the logs during post-analysis. 

====Note to get full logging of the services, you must enable both server and client. The introspection info is attached by both the server and client. With only one side introspection enabled, there may be partial information, or none depending on the implementation. ====
