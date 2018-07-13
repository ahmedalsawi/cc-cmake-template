## Configuration
cmake_minimum_required (VERSION 2.6)
set(PROJECT_NAME cc-proj)
project (${PROJECT_NAME} CXX C)

set (CMAKE_VERSION_MAJOR 1)
set (CMAKE_VERSION_MINOR 0)

LIST(APPEND CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake")

include(git) 

## prebuild
configure_file (
  "${PROJECT_SOURCE_DIR}/src/gconfig.h.in"
  "${PROJECT_BINARY_DIR}/gconfig.h"
  )
include_directories("${PROJECT_BINARY_DIR}")

include_directories("${PROJECT_SOURCE_DIR}/external")
include_directories("${PROJECT_SOURCE_DIR}/src")


set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall")
message(STATUS "NOTE: Running with extra CXX_FLAGS ${CMAKE_CXX_FLAGS}")

set(CMAKE_CXX_FLAGS_DEBUG "-O0 -ggdb")
set(CMAKE_CXX_FLAGS_RELEASE "-O3  -DNDEBUG")

if (NOT  CMAKE_BUILD_TYPE)
	set(CMAKE_BUILD_TYPE  debug)
endif()
message(STATUS "NOTE: Build configuration: " ${CMAKE_BUILD_TYPE}) 


include_directories ("${PROJECT_SOURCE_DIR}/src/common")
include_directories ("${PROJECT_SOURCE_DIR}/src/dyn-common")
add_subdirectory ("${PROJECT_SOURCE_DIR}/src/common") 
add_subdirectory ("${PROJECT_SOURCE_DIR}/src/dyn-common") 

#add_subdirectory("${PROJECT_SOURCE_DIR}/external/googletest")

if (CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
	set (CMAKE_INSTALL_PREFIX "${PROJECT_BINARY_DIR}/${PROJECT_NAME}" CACHE PATH "default install path" FORCE )
endif()
message(STATUS "NOTE: Install Path: " ${CMAKE_INSTALL_PREFIX}) 

## Add support for shared libraries in install path
SET(CMAKE_SKIP_BUILD_RPATH  FALSE)
SET(CMAKE_BUILD_WITH_INSTALL_RPATH FALSE) 
SET(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/lib")
SET(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)

## build
add_executable(main src/main.cc)
target_link_libraries (main common dyn-common)


## post-build (install)
install (TARGETS main DESTINATION ${CMAKE_INSTALL_PREFIX}/bin)
install (TARGETS dyn-common DESTINATION ${CMAKE_INSTALL_PREFIX}/lib)
