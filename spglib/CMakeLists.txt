
###
### Project settings
###
project(Spglib)

set(SPGLIB_VERSION_MAJOR "1")
set(SPGLIB_VERSION_MINOR "2")
set(SPGLIB_VERSION "${SPGLIB_VERSION_MAJOR}.${SPGLIB_VERSION_MINOR}")

###
### Configuration settings
###
# Offer the user the choice of overriding the installation directories
set(INSTALL_LIB_DIR lib CACHE PATH "Installation directory for libraries")
set(INSTALL_INCLUDE_DIR include CACHE PATH  "Installation directory for header files")
set(INSTALL_ARCHIVE_DIR lib CACHE PATH  "Installation directory for header files")
if(WIN32 AND NOT CYGWIN)
  set(DEF_INSTALL_CMAKE_DIR CMake)
else()
  set(DEF_INSTALL_CMAKE_DIR lib/CMake/spglib)
endif()
set(INSTALL_CMAKE_DIR ${DEF_INSTALL_CMAKE_DIR} CACHE PATH "Installation directory for CMake files")

# Make relative paths absolute (needed later on)
foreach(p LIB INCLUDE ARCHIVE CMAKE)
  set(var INSTALL_${p}_DIR)
  if(NOT IS_ABSOLUTE "${${var}}")
    set(${var} "${CMAKE_INSTALL_PREFIX}/${${var}}")
  endif()
endforeach()

###
### Sources, headers, directories and libs
###
set(header_directory "include/spglib/")

set(spglib_Header_Files__
  include/spglib/cell.h
  include/spglib/debug.h
  include/spglib/hall_symbol.h
  include/spglib/kpoint.h
  include/spglib/lattice.h
  include/spglib/mathfunc.h
  include/spglib/pointgroup.h
  include/spglib/primitive.h
  include/spglib/refinement.h
  include/spglib/site_symmetry.h
  include/spglib/sitesym_database.h
  include/spglib/spacegroup.h
  include/spglib/spg_database.h
  include/spglib/spglib.h
  include/spglib/spin.h
  include/spglib/symmetry.h
)
source_group("Header Files\\" FILES ${spglib_Header_Files__})

set(spglib_Source_Files__
  src/cell.c
  src/debug.c
  src/hall_symbol.c
  src/kpoint.c
  src/lattice.c
  src/mathfunc.c
  src/pointgroup.c
  src/primitive.c
  src/refinement.c
  src/site_symmetry.c
  src/sitesym_database.c
  src/spacegroup.c
  src/spg_database.c
  src/spglib.c
  src/spglib_f.c
  src/spin.c
  src/symmetry.c
)
source_group("Source Files\\" FILES ${spglib_Source_Files__})

set(spglib_Files
  ${spglib_Header_Files__}
  ${spglib_Source_Files__}
)

include_directories(include/spglib)

##
## General install settings
##
# Need to add this as the c files in spglib are used to living in same
# folder as header files
add_library(spglib STATIC ${spglib_Files})

# Suppress spglib warnings
set_target_properties(spglib PROPERTIES COMPILE_FLAGS "-w")
           
set_target_properties(spglib PROPERTIES PUBLIC_HEADER "${spglib_Header_Files__}")

install(TARGETS spglib
  EXPORT SpglibDepends
  LIBRARY DESTINATION "${INSTALL_LIB_DIR}" COMPONENT shlib
  PUBLIC_HEADER DESTINATION "${INSTALL_INCLUDE_DIR}/spglib" COMPONENT dev
  ARCHIVE DESTINATION "${INSTALL_ARCHIVE_DIR}" COMPONENT stlib
)


###
### Configuration file settings
###
# Add all targets to the build-tree export set
export(TARGETS spglib FILE "${PROJECT_BINARY_DIR}/SpglibDepends.cmake")
 
# Export the package for use from the build-tree
# (this registers the build-tree with a global CMake-registry)
export(PACKAGE Spglib)
 
# Create a SpglibBuildTreeSettings.cmake file for use from the build tree
configure_file(CMake/SpglibBuildTreeSettings.cmake.in
  "${PROJECT_BINARY_DIR}/SpglibBuildTreeSettings.cmake" @ONLY)
 
# Create the SpglibConfig.cmake and SpglibConfigVersion.cmake files
file(RELATIVE_PATH CONF_REL_INCLUDE_DIR "${INSTALL_CMAKE_DIR}"
   "${INSTALL_INCLUDE_DIR}")
   
configure_file(CMake/SpglibConfig.cmake.in
  "${PROJECT_BINARY_DIR}/SpglibConfig.cmake" @ONLY)
configure_file(CMake/SpglibConfigVersion.cmake.in
  "${PROJECT_BINARY_DIR}/SpglibConfigVersion.cmake" @ONLY)
 
# Install the SpglibConfig.cmake and SpglibConfigVersion.cmake
install(FILES
  "${PROJECT_BINARY_DIR}/SpglibConfig.cmake"
  "${PROJECT_BINARY_DIR}/SpglibConfigVersion.cmake"
  DESTINATION "${INSTALL_CMAKE_DIR}" COMPONENT dev)
 
# Install the export set for use with the install-tree
install(EXPORT SpglibDepends DESTINATION "${INSTALL_CMAKE_DIR}" COMPONENT dev)

