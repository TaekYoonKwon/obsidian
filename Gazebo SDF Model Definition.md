A model, which describes a single entity in Gazebo, is made up of links which are primitives that make up a model, and joints which describes how each link is connected to one another, and a plugin which injects a custom logic to how the model interacts.
Following the [[Entity-Component System (ECS) architecture]], models, links, joints are all its unique **Entities**. We attach **Components** to each entity to describe its properties. The model is made up of several components, each of which is a link, Using joint, Physics system applies logic to the components, and plugin defined a new system to load to act on the components. 
>[!INFORMATION] Links and Joints
>It is often not required to define a joint for static objects. Joints describe how each link is connected and by declaring the model static, we are essentially "gluing" all the links as static joints. However, in practice, if we have a rigid part of the model that do not move relative to each other, it is often better to define them as a single link rather than multiple links joined by a fixed joint. Each link declaration, even if they are declared as rigidly linked via a fixed joint, will add a new constraint to the physics solver. It significantly improves simulation performance to use a single link instead. 

To define a link, following properties may be declared:
- Collision
	  This field captures the geometry used to evaluate for collision. It is preferred to approximate the collision geometry as simple shapes, rather than a complex mesh for optimising the simulation performance. May have more than one collision for a link.
- Visual
	  This field is used to render the link. May have more than one visual for a link
- Inertial
	 This field describes the dynamic properties of the link for the physics engine. Includes fields like mass and rotational inertia.
- Sensor
	 Collects Data from the world for use in plugins. 

## Inertial Properties Definitions
 The crucial distinction about the pose field in inertial frame is that this pose describes the relative pose between the centre of mass frame of the link and the link frame. The link frame is defined by the pose declared under the link. 
 ```SDF
 <link name='left_wheel'>
	<pose relative_to='chassis'>1.0 0.5 0 0 0 0 </pose>
 ```
 This pose declares the link frame - this is the "centre" of the component as an entity. This is the frame that will be used when mounting the joint, so the position of the link frame determines whether a link spins about the end or the centre etc. 

But critically, most CAD programs that design the mesh, do not have a universal notation around the sign convention of product of inertia. To make sure having this sign mismatch between the CAD program and Gazebo, we prefer to remove product of inertia entirely, and for that, we must align the inertial frame with the **principal axis of inertia**.
To align the inertial frame with the principal axis of inertia, we declare pose under inertial tag, like so:
```SDF
    <inertial>
        <mass>1</mass>
        <pose> 0 0 0 1.57 0 -1.57 </pose>
        <inertia>
            <ixx>0.043333</ixx>
            <ixy>0</ixy>
            <ixz>0</ixz>
            <iyy>0.043333</iyy>
            <iyz>0</iyz>
            <izz>0.08</izz>
        </inertia>
```
Here the pose tag transforms the inertial frame from the link frame to the principal axis of inertia, so that the product of inertia can be 0. All of this information is usually available via CAD software. 