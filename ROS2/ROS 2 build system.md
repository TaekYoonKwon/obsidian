To build a workspace, use [[Colcon]], which manages the building of many packages together. It manages which package to build first, setting up the build environment and solving the issues with dependencies, with information from [[package.xml]]. 

A single workspace can contain as many packages as wanted, each in their own folder. Nested [[package]] is not possible. A single workspace may look like this:
``` 
workspace_folder/
    src/
      cpp_package_1/
          CMakeLists.txt
          include/cpp_package_1/
          package.xml
          src/

      py_package_1/
          package.xml
          resource/py_package_1
          setup.cfg
          setup.py
          py_package_1/
      ...
      cpp_package_n/
          CMakeLists.txt
          include/cpp_package_n/
          package.xml
          src/
```

To build a single [[package]] with a skeleton [[node]]:
`ros2 pkg create --build-type ament_cmake --license Apache-2.0 --node-name my_node my_package` 

Creating a [[package]] will simply create a folder inside src/, containing [[CMakeLists]].txt, [[package.xml]], src folder with cpp file inside it and include folder with a header file. 

For ROS2, in order to build the workspace, which is the whole project, the tool needs to build all of the included packages. The tool needs to know how to set up the build environment for the [[package]], invoke the build. 

How to create, build and run ROS2 workspace:
1. Create workspace:
2. Add packages to src/
3. Source the ***underlay*** environment
	`source /opt/ros/foxy/setup.bash`
4. Build the workspace
	`colcon build` at the root of the workspace
5. Source the ***overlay***
	`source install/local_setup.bash`
6. Run the new nodes, [[actions]] etc.
	`colcon build --packages-select my_package`
====***Underlay*** is like the standard library layer, and ***Overlay*** is like the application code, which depend on the ***underlay***. ====

The reason we need to source the underlay and overlay environments is that this process executes shell scripts which set **environment variables** (such as `AMENT_PREFIX_PATH`, `PATH`, `LD_LIBRARY_PATH`, etc.).  
This makes local packages and nodes **discoverable** to ROS 2 tooling (`ros2 run`, `ros2 launch`, etc.).

Sourcing the overlay _after_ the underlay is critical, because the overlay **stacks on top** of the previously sourced environment.  
If there are packages with the same name in both the overlay and underlay, the version in the overlay will take precedence — as it typically represents your **application layer** or **locally modified packages** that should override the system version.

### Project Directory
- `src/`
	Source code of all packages
- `build/`
	Temporary build files for each [[package]]
- `install/`
	Final installed binaries, libraries, launch files etc
- `log/`
	Logs of the build. Useful for debugging.