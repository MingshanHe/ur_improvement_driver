cmake_minimum_required(VERSION 2.8.3)
project(ur_modern_driver)
add_definitions( -DROS_BUILD )
## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
  hardware_interface
  controller_manager
  actionlib
  control_msgs
  geometry_msgs
  roscpp
  sensor_msgs
  std_msgs
  trajectory_msgs
  ur_msgs
  tf

  controller_interface
  forward_command_controller
  control_toolbox
  realtime_tools
  urdf
  kdl_parser
  kdl_conversions
  cartesian_state_msgs
)

catkin_package(
  INCLUDE_DIRS include
#  LIBRARIES ur_modern_driver
  CATKIN_DEPENDS hardware_interface controller_manager actionlib control_msgs geometry_msgs roscpp sensor_msgs trajectory_msgs ur_msgs
  DEPENDS ur_hardware_interface
  DEPENDS cartesian_state_msgs
)

###########
## Build ##
###########

# check c++11 / c++0x
include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)
CHECK_CXX_COMPILER_FLAG("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
if(COMPILER_SUPPORTS_CXX11)
    set(CMAKE_CXX_FLAGS "-std=c++11")
elseif(COMPILER_SUPPORTS_CXX0X)
    set(CMAKE_CXX_FLAGS "-std=c++0x")
else()
    message(FATAL_ERROR "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Please use a different C++ compiler. Suggested solution: update the pkg build-essential ")
endif()

include_directories(include 
  ${catkin_INCLUDE_DIRS}
)

add_library(ur_hardware_interface src/ur_hardware_interface.cpp)
target_link_libraries(ur_hardware_interface
  ${catkin_LIBRARIES}
)

set(${PROJECT_NAME}_SOURCES
    src/ur_ros_wrapper.cpp
    src/ur_driver.cpp
    src/ur_realtime_communication.cpp
    src/ur_communication.cpp
    src/ur_dashboard.cpp
    src/robot_state.cpp
    src/robot_state_RT.cpp
    src/do_output.cpp
    src/ur_ros_wrapper.cpp)
add_executable(ur_driver ${${PROJECT_NAME}_SOURCES})

add_dependencies(ur_driver ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})


target_link_libraries(ur_driver
  ur_hardware_interface
  ${catkin_LIBRARIES}
 )


install(DIRECTORY launch/ DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/launch)

## Mark executables and/or libraries for installation
install(TARGETS ur_driver ur_hardware_interface
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

## Mark cpp header files for installation
install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
)