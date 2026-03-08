cmake_minimum_required(VERSION 3.20 FATAL_ERROR)

project(Compressonator VERSION 5.0.0)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_subdirectory(cmp_core)
add_subdirectory(cmp_compressonatorlib)
