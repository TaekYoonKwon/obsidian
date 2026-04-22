`package.xml` is the package manifest, which declares its dependencies, so that [[ROS 2 build system]] can figure out what the [[package]] needs.
- what your package **needs to build and run**,
- what **other packages depend on you**,
- what version your package is,
- and general info like maintainer, license, etc.

Main purpose of package.xml is as following:
- **Dependency declaration** : informs ROS 2 build system what the target package needs.
- **Tooling and discovery** : Tools like [[Rosdep]], [[Colcon]]
- **ROS metadata** : Includes information such as version, license, Readme etc.

When a [[Service Type]] or [[Topic Type]] is added or updated, the package manifest file must be updated if the updated type includes dependencies from external ROS types:
- `<build_depend>`
	Specifies dependencies needed during the **build** phase. This is essential for packages that generate code or need to compile with specific dependencies, such as `rosidl_default_generators` for custom message or service types.
- `<exec_depend>`
	Specifies dependencies needed at **runtime**. These dependencies are necessary for the functionality of your package when it is running. For instance, if your package interacts with another package like `rclcpp` or a custom service type, you’d include it here.
- `<test_depend>`
	Specifies dependencies required for **testing**. If you use testing tools such as `ament_lint`, `gtest`, or any other testing framework, they go in here.

