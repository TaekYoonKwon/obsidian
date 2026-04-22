The fundamental question for a topological build system is how to make sure the build directories are established and sourced that building a dependency and find the [[package]] successfully.

In other words:
- Before building a [[package]], **all its dependencies** (other packages) as indicated by [[CMakeLists]].txt must be **already built and sourced**.    
- Otherwise, CMake will fail because it cannot find the headers, libraries, or configurations it needs.

Colcon solves this by **analyzing the dependency graph** (based on [[package.xml]] information) and **building in the correct topological order** automatically.
1. `colcon` searches inside the src/ folder from the directory where it was called, recursively navigating. It looks through each [[package]] folder inside the src folder to locate what available packages are there for `colcon` to build.
2. `colcon` looks at the `package.xml` files of all packages.
	- It figures out "which package depends on which other package" (directly or indirectly).
2. **Topological Sorting**:
    - It sorts the packages into a **build order** where no package is built before its dependencies are ready.
3. **Build Execution**:
    - Colcon builds each package one-by-one following the sorted order.
    - After each package is built:
        - It creates a "local install" inside the `install/` directory.
        - The environment is **overlaid** (temporarily sourced) with this install before moving on to the next package.
4. **Isolated Workspaces**:
    - Each package build is kept as independent as possible.
    - This isolation makes sure that:
        - Build errors are easier to trace.
        - Packages don't accidentally interfere with each other.

- **List Available Packages**
	- `colcon list`
- **Selective Builds**:
    - `colcon build --packages-select <package_name>` 
- **Skipping Packages**:
    - `colcon build --packages-skip <package_name>` 
- **Parallel Building**:
    - `colcon build --parallel-workers 20`
- **Continued Building After Errors**:
	- `colcon build --continue-on-error`
- **Clean Builds**:
	- `rm -rf build/ install/ log/`


