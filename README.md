# Demystifying CMake

- CMake config is created in a file called `CMakeLists.txt`.
- First create a build directory and then execute `cmake ..` and then use `make`  from build directory to separate build artifacts.
- First line is usually `cmake_minimum_required` which checks if we the current version is above a certain specified version
```cmake
cmake_minimum_required(VERSION 3.8)
```
- Then we add a line to specify project name
```cmake
project(myproject)
```

### Basic Example
- In case of simple project with one executable we can use this
- add_executable takes the executable file name(`main.cpp`) to generate an executable and the current project name specified using `project()` which is used as the name of the executable
```cmake
cmake_minimum_required(VERSION 3.16)
project(myproject)

add_executable(${PROJECT_NAME} main.cpp)
```

## Building executable and libraries
- use `add_library` to add libraries
```cmake
cmake_minimum_required(VERSION 3.8)

project(learning_cmake)

add_library(my_lib lib.cpp)

add_executable(${PROJECT_NAME} main.cpp)
```
- `add_library` command adds a static or dynamic library, which here is my_lib. You also provide the command with the source files.
- By default this command builds a static library. All `.a` files built by CMake are static libraries. You can also explicitly ask CMake to build a static library by using the keyword `STATIC`.
```cmake
add_library(mylib STATIC lib.cpp) // Will create a libmylib.a file
```
- You can tell CMake to build a `DYNAMIC` library by using the keyword `SHARED` and this will create a `.so` file.
```cmake
add_library(mylib SHARED lib.cpp) // Will create a libmylib.so file
```

## Using project libraries
- Now after building our project libraries we link the library to our target which in this case is learning_cmake which we get from `${PROJECT_NAME}`.
```cmake
target_link_libraries(${PROJECT_NAME} mylib)
```

## Using sub-directories
- What happens when your library has it's own sub-directory?
- Assume your library files live the a separate sub-directory called lib. This directory has `lib.cpp` `lib.hpp`.
- Now we remove the `add_library` function from the main project `CMakeLists.txt` file and create a separate `CMakeLists.txt` file in the `lib` directory.
```cmake
#CMakeLists.txt for lib directory
add_library(mylib lib.cpp)

target_include_directories(mylib
	INTERFACE
	${CMAKE_CURRENT_SOURCE_DIR}
	)
```
- `add_library` builds the library but the main project `CMakeLists.txt` cant find `lib.hpp` and we need to use `target_include_directories` to include `mylib` to the include path of the main project.
- `target_include_directories` needs the target(`mylib`), visibility(`INTERFACE or PRIVATE or PUBLIC`) and the library source.
	- `PRIVATE` : It is used only used for the current target which here is `mylib` but not used by any dependencies
	- `PUBLIC`: It is used by both current target(`mylib`) and dependencies.
	- `INTERFACE`:It is only used by dependencies and not by the current target(`mylib`) itself.
## Finding and adding system libraries
- To include system headers first use `find_package()` and then link using `target_link_libraries`. Take [fmt](https://fmt.dev/11.0/get-started/) for example.
```cmake
cmake_minimum_required(VERSION 3.8)
project(learn_cmake)

find_package(fmt)
add_subdirectory(lib)
add_executable(${PROJECT_NAME} main.cpp)

target_link_libraries(${PROJECT_NAME} mylib fmt::fmt)
```

## Setting C++ Standards
- You can set and force c++ standards using these commands
```cmake
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED YES)
set(CMAKE_CXX_EXTENSIONS OFF)
```
- `CMAKE_CXX_STANDARD` is used to set the desired c++ version
- `CMAKE_CXX_STANDARD_REQUIRED` If set to `YES` then cmake forces the compilation to happen using the version set by `CMAKE_CXX_STANDARD`. If set to `NO` or not set then cmake tries to compile with available versions of c++ if the `CMAKE_CXX_STANDARD` is not available.
- `CMAKE_CXX_EXTENSIONS` is used to allow or not allow extensions from other other compilers. For example we can pull in extensions from g++ here and use them if this is set to `ON`
