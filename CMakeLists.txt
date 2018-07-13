## Configuration
cmake_minimum_required (VERSION 2.6)
project (cc-proj CXX C)

set (CMAKE_VERSION_MAJOR 1)
set (CMAKE_VERSION_MINOR 0)

## from http://xit0.org/2013/04/cmake-use-git-branch-and-commit-details-in-project/
## install cmake module
execute_process(
  COMMAND git rev-parse --abbrev-ref HEAD
  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
  OUTPUT_VARIABLE GIT_BRANCH
  OUTPUT_STRIP_TRAILING_WHITESPACE
)

# Get the latest abbreviated commit hash of the working branch
execute_process(
  COMMAND git log -1 --format=%h
  WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
  OUTPUT_VARIABLE GIT_COMMIT_HASH
  OUTPUT_STRIP_TRAILING_WHITESPACE
)

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
add_subdirectory ("${PROJECT_SOURCE_DIR}/src/common") 

add_subdirectory("${PROJECT_SOURCE_DIR}/external/googletest")

## build
add_executable(main src/main.cc)
target_link_libraries (main common gtest)



## post-build (install)
install (TARGETS main DESTINATION ${PROJECT_BINARY_DIR}/bin)
