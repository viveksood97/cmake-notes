cmake_minimum_required(VERSION 3.8)
project(learn_cmake)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED YES)
set(CMAKE_CXX_EXTENSIONS OFF)

find_package(fmt)
add_subdirectory(lib)
add_executable(${PROJECT_NAME} main.cpp)

target_link_libraries(${PROJECT_NAME} mylib fmt)
