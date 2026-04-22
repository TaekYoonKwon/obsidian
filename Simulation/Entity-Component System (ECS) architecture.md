# The Problem with OOP
In traditional OOP paradigm, it was common to have a monolith class which describes everything about an entity. Such class would look something like:
```
class Robot : common::GeneralSystem {
	public:
	[[nodiscard]] bool init(void);
	[[nodiscard]] common::SuccessCode link(const bridge::base_bridge br);
	private:
	[[nodiscard]] bool check_system_engage(void);
	bool online{};
	double UUID{};
}
```
Where the class holds data, logic, and everything. This is problematic for a number of reasons.
1. Adding a new feature means messing with the existing members, and methods.
2. Inheritance hierarchy could become complicated, in trying to define common methods and members.

# Entity-Component System (ECS) 
There are three key concepts in ECS architecture:
1. Entities
   This is just an ID, a number which helps for the processes to identify who is who. This is completely agnostic to what the associated entity is.
2. Components
   This is a pure data bag, attached to entities. It only has member variables, no logic or methods. For example, Entity #2391 may define a pose, Entity #2012 may define the visual, Entity #2122 may define the Link. The combinations of components define what something is.
3. Systems
   This is where the logic lives. A system may operate and apply logic to any set of components. Physics system may grab every entity with Pose, Inertial, Collision to step the dynamics. Rendering system may grab every entity with Visual and Pose, to draw them on the screen. 

## Where ECS shines
There is a strict separation of concern with ECS - system is agnostic of what set of components it is applying the logic to other than the fact that they have all the set of components. With this separation, adding a new system is trivial as it effortlessly applied to and interact with components. 