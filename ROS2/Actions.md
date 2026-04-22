Actions are one of communication types in ROS2, intended for ***long running tasks***. Action is a collection of [[services]] and [[topics]], which allows for a nice blend of client-server interaction and asynchronous feedback.

Unlike Services, Actions can be cancelled before concluding, and also they provide continuous feedback as opposed to services which return a single and final response.

Three central parameters for actions:
1. Goal (Service)
2. Feedback (Topic)
3. Result (Service)

For an action, there exists nodes which are action client and action server, which are similar to service client and service server.
- Action Client will:
	- Send a goal request to the action server.
	- Receive the feedback topic throughout the progress.
	- Receive the result response from the action server.
- Action server will:
	- Receive a goal request from the action client.
	- Continuously publish progress through the feedback topic.
	- Send a result response to the action client when finished.

## `Turtlesim` Example
`Turtlesim` and `turtle_teleop_key` are a good example of action usage in ROS2. 

`Turtlesim` is the action server, and `turtle_teleop_key` is the action client. `turtle_teleop_key` sends goals to `turtlesim`, as indicated by the terminal message:
``` 
Use arrow keys to move the turtle.
Use G|B|V|C|D|E|R|T keys to rotate to absolute orientations. 'F' to cancel a rotation.
```
Any of the keyboard press from the list displayed above will generate a goal request to `turtlesim`.

====Note: for `turtle_teleop_key`, we observe if we issue another action request before the previous action request commences, `turtlesim` will abort the previous one to immediately start executing the new action request. This behaviour around new command handling is implementation specific, and cannot be assumed for any action server. ====

## Interaction with Actions via CLI 
- `ros2 action send_goal <action_name> <action_type> <values> [--feedback]`
- 