

cmake_minimum_required(VERSION 2.6)

project(CSLibs)
set_property(TARGET PROPERTY PROJECT_LABEL "Common Structure Libraries")

message(STATUS "Configuring common structure libraries.")

add_subdirectory(yaml-cpp)
# Have to unset this variable as both libraries use it and it is cached so
# the second would pick it up from the first
unset(INSTALL_CMAKE_DIR CACHE)
add_subdirectory(spglib)
