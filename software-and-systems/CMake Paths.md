CMake is basically a recipe bulider for compiling applications. In CMake, you may want to include external CMakeFiles, or external libraries, that may not be in the same file or directories. For example, it is pretty common for a project to have

some-project/CMakeLists.txt
some-project/src/module1/CMakeLists.txt

So in building this project, developers would build using the CMakeLists.txt that are on the repository root, but that CMakeLists.txt has to know about the CMakeLists.txt in sub-directories. For this, you just use `add_subdirectory(path)`.  Using this, you can keep the CMakeLists.txt at a manageable length and also modularise your build so that at each step, CMakeLists.txt only cares about that layer and what it needs to know.

But for [[CMake Module]] files (.cmake), which basically has helper functions, you need to specify where to look. Because CMakeLists.txt will include it like so:
```cmake
include(exampleCmake)
``` 
So cmake has to know, where that `exampleCmake.cmake` file is. 

Then we have cases when we want to link libraries, which are basically compiled object files that have not been linked to a binary.So for modularised build, subsequent CMakeLists.txt files would produce libraries, which then the root CMakeLists.txt link against. This is common in external packages that we download too. when you APT install, packages may put .so files in the installation destination. 

To find external libraries, we would typically go:
```CMake
find_package(some_package)
```
> [!INFORMATION] Distinction between **find_package** and **include** 
> find_package and include both looks for `.cmake` files. 
> The difference is, include() is like a raw read, where we just include it into the current CMakeLists as if it was inline. 
> But find_package is include + validation + register. It validates it against the version of the library that was declared as **REQUIRED** - find_package(Foo 2.0 REQUIRED)
> For any raw .cmake file we want to include it in the build, we use **include()**
> For an actual library we want to include, we use **find_package()** 

# CMAKE_MODULE_PATH
**CMAKE_MODULE_PATH** is where CMake looks for standalone `.cmake` scripts - files you `include()` directly, or `Find<Pkg>.cmake` find modules used by `find_package` in module mode.
# CMAKE_PREFIX_PATH
**CMAKE_PREFIX_PATH** basically specifies where the CMake can find the external packages. 

`find_package` _also_ respects `CMAKE_MODULE_PATH` (in module mode), and `include()` _only_ respects `CMAKE_MODULE_PATH`, never `CMAKE_PREFIX_PATH`. 
So the two variables aren't symmetric - `CMAKE_PREFIX_PATH` is purely a find_package concept; `CMAKE_MODULE_PATH` is used by both commands.

Here the important distinction about find_package is that find_package simply looks through the `.cmake` metadata files to learn about the libraries. Where the header files are, what target name to use, what to link against etc. It doesn't actually load or link yet. It just knows that it exists somewhere and what that library is. 
This find_package has two strategies: 
1. Config mode: This is for when the library has its own `.cmake` file written. The directory in which the  `.cmake` file is written, is under the same install _prefix_ as where the library is located. So we can simply go to **CMAKE_PREFIX_PATH** to find the library and `.cmake` file basically together. 
2. Module mode: This is when the library does not have its own `.cmake` script. So the `.cmake` script is detached from where the library files are actually located. Then we resort to **CMAKE_MODULE_PATH** to find the `.cmake` file, which will hopefully tell us where to find the header files, what to link against etc. 