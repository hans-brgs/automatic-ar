# ----------------------------------------------------------------------------
#   Basic Configuration
# ----------------------------------------------------------------------------
cmake_minimum_required(VERSION 3.0)
project(sgl VERSION "1.0.0" LANGUAGES CXX)
set(PROJECT_SOVERSION "${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}")
set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_CXX_STANDARD 11) # C++11...
set(CMAKE_CXX_STANDARD_REQUIRED ON) #...is required...
set(CMAKE_CXX_EXTENSIONS ON) #...with compiler extensions like gnu++11

add_definitions(-DAPP_SGLVIEWER_DSO_EXPORTS)

#include(cmake/options.cmake)
#include(cmake/findDependencies.cmake)

#FIND DEPENDENCIES

set(CMAKE_AUTOMOC ON)
set(CMAKE_INCLUDE_CURRENT_DIR ON)

if(DEFINED ENV{QT6_CMAKE_PATH})
    set(CMAKE_PREFIX_PATH $ENV{QT6_CMAKE_PATH})
endif()
set(CMAKE_PREFIX_PATH "C:/Qt/6.5.0/msvc2019_64/lib/cmake")

find_package(Qt6 COMPONENTS Widgets)
find_package(OpenCV REQUIRED)
include_directories( ${OpenCV_INCLUDE_DIRS} )
#message(STATUS "OpenCV found, version: ${OpenCV_VERSION} in dir ${OpenCV_INCLUDE_DIRS}")


include(cmake/compilerOptions.cmake)

add_subdirectory(src)
add_subdirectory(utils)

include(cmake/printInfo.cmake)

