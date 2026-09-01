# Definition
Any `.cmake` file which contains CMake code is a CMake module. This means `.cmake` files are created with modularity by design, with shareable definitions and declarations across project(s).

Basically like a python import, CMake module, syntax wise, is identical to CMakeLists.txt. The only difference lies in that when we invoke a build and we call `cmake ..`, it looks for CMakeLists.txt. 

But to run this module by itself, we can do `cmake -P script.cmake` to run the standalone file and it will run the module as script mode. In script mode, any implications of a project or a structure such as subdirectory, add_executable, install targets cannot run.  

The reason we don't see this very often is that conventionally, `.cmake` file is regarded as a **reusable script**, and `CMakeLists.txt` is the entrypoint. Likewise, nothing stopping you from performing an `include()` on a `CMakeLists.txt` from a `script.cmake` file, since `include()` is agnostic of the file extension type. It just includes what it sees. 

# Types of Modules
There are three main types of modules:
1. Utility Modules
   Included in the project itself, usually under `cmake/` directory. This has all the utilities a project may need. This is typically integrated via `include()` 
2. Find Modules
   This is a helper module that will be invoked to find a shared library file that may be hiding somewhere. This is for projects that don't ship their own `.cmake` files, that there is nothing telling them where the files actually are and what we can expect from them etc. Probably has a combination of `find_package()`, `find_path()`, `find_library()` or `find_program()`. Refer to [[CMake Paths]]
3. Config File (technically not a module at this point)
   This is a module that was shipped by a library to describe itself. `find_package()` finds this and use this to tell what to expect about the library. This is basically a find module that doesn't need to hunt it down.

# Practical stuff
- **`include_guard(GLOBAL)`** at the top prevents double-include errors when the same module ends up included from multiple paths. Like header guards in C++.
- **Variables and functions defined in a `.cmake` file leak into the caller's scope** when included. That's usually what you want (it's why you included it), but it means modules can clobber the caller's variables. Use `function()` for things you don't want to leak; `macro()` is textual and shares the caller's scope.