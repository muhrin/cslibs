

cmake_minimum_required(VERSION 2.6)

project(CSLibs)
set_property(TARGET PROPERTY PROJECT_LABEL "Common Structure Libraries")

message(STATUS "Configuring common structure libraries.")

add_subdirectory(spglib)
add_subdirectory(yaml-cpp)
