- Gazebo sim runs a simulation by launching two processes:
  1. Backend server (GZ Server) `gz sim -s`
  2. Frontend client process (GZ GUI) `gz sim -g`
- There is no big binary which runs everything. It is split into separate executables which talk over a network via [[Gazebo Transport]].
## Server Process:
The server runs an [[Entity-Component System (ECS) architecture]]. 

The backend has multiple systems which are responsible for all the logic with the simulation. Computing physics and stepping the dynamics, receiving user commands etc.
The fundamental principles are that the server process just blindly runs all the registered plugins, with each plugin only aware of the components and entities available, not other plugins.
Note `gz-physics`, `gz-sensors` etc are libraries - they technically live above ECS architecture, and they interact with the server as a system, via a wrapper such as `gz::sim::systems::Physics` etc.

The server process is a looped process, which loops and runs all the registered systems. This is called the **Simulation Loop**. 
### Quick dive into determinism
The problem that may arise here is that, if we have multiple systems that are writing to an entity, and also multiple systems that are reading from the same entity, will we not run into a race condition?? The answer is that [[EntityComponentManager]] (ECM) handles this. Systems never directly interact with entities - instead they query and update via ECM, which ensures strict boundary between applying changes and reading the current status of the entity. This ensures that every system which requires an observation, will do so after all effects have been applied.

### Custom Plugins
The plugins we write are systems - they implement and live inside ECS loop with [[ISystemUpdate]].  They get called every simulation step, with access to the [[EntityComponentManager]]. 

## Communication Process
Any information that needs to cross the boundary of a process - server to GUI has to go through the communication library. 

Synchronisation between processes are handled by the processes themselves. For example, communication from server to GUI is managed by Scene Broadcaster - a server side Gazebo Sim plugin which uses [[Gazebo Transport]] and [[Gazebo Messages]] libraries to send messages from server to the client. 

## Frontend Client Process (GUI)
The client side process is namely [[Gazebo GUI]], which is what renders the entities via [[Gazebo Rendering]] and their components, arrived via communication process.

There are also many other client processes other than the GUI. Frontend plugins typically communicate among themselves via events, which are similar to messages, but processed synchronously. For example, the Render event emitted by a 3D scene process can be caught by other processes, and since this is synchronous, other client processes which may edit the 3D scene before it gets rendered. 

Frontend plugins have access to entities and components via [[EntityComponentManager]] on the client side. Client side plugins only react to the entities and components information, rather than acting on it.

Note this is separate to the Server side ECM. So the way it works is typically - Scene Broadcaster or another process packages the states of the entities and components, then publishes it over [[Gazebo Transport]] as a message. This is then processed by ECM on the client side, which unpacks the messages and distributes among the client side processes. 

