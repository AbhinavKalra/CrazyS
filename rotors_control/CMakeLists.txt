cmake_minimum_required(VERSION 2.8.3)
project(rotors_control)

add_definitions(-std=c++11)

find_package(catkin REQUIRED COMPONENTS
  geometry_msgs
  mav_msgs
  nav_msgs
  roscpp
  sensor_msgs
  cmake_modules
)

find_package(
  Eigen REQUIRED
)

catkin_package(
  INCLUDE_DIRS include ${Eigen_INCLUDE_DIRS}
  LIBRARIES lee_position_controller position_controller crazyflie_onboard_controller roll_pitch_yawrate_thrust_controller sensfusion6 complementary_filter_crazyflie2
  CATKIN_DEPENDS geometry_msgs mav_msgs nav_msgs roscpp sensor_msgs
  DEPENDS Eigen
)


include_directories(
  include
  ${catkin_INCLUDE_DIRS}
  ${Eigen_INCLUDE_DIRS}
)

add_library(lee_position_controller
  src/library/lee_position_controller.cpp
)

add_library(position_controller
   src/library/position_controller.cpp
)

add_library(crazyflie_onboard_controller
   src/library/crazyflie_onboard_controller.cpp
)

add_library(roll_pitch_yawrate_thrust_controller
  src/library/roll_pitch_yawrate_thrust_controller.cpp
)

add_library(complementary_filter_crazyflie2
  src/library/complementary_filter_crazyflie2.cpp
)

add_library(sensfusion6
  src/library/sensfusion6.cpp
)

target_link_libraries(lee_position_controller ${catkin_LIBRARIES})
add_dependencies(lee_position_controller ${catkin_EXPORTED_TARGETS})

target_link_libraries(position_controller ${catkin_LIBRARIES})
add_dependencies(position_controller ${catkin_EXPORTED_TARGETS})

target_link_libraries(roll_pitch_yawrate_thrust_controller ${catkin_LIBRARIES})
add_dependencies(roll_pitch_yawrate_thrust_controller ${catkin_EXPORTED_TARGETS})

target_link_libraries(crazyflie_onboard_controller ${catkin_LIBRARIES})
add_dependencies(crazyflie_onboard_controller ${catkin_EXPORTED_TARGETS})

target_link_libraries(complementary_filter_crazyflie2 ${catkin_LIBRARIES})
add_dependencies(complementary_filter_crazyflie2 ${catkin_EXPORTED_TARGETS})

target_link_libraries(sensfusion6 ${catkin_LIBRARIES})
add_dependencies(sensfusion6 ${catkin_EXPORTED_TARGETS})

add_executable(lee_position_controller_node src/nodes/lee_position_controller_node.cpp)
add_dependencies(lee_position_controller_node ${catkin_EXPORTED_TARGETS})
target_link_libraries(lee_position_controller_node
  lee_position_controller ${catkin_LIBRARIES})

add_executable(position_controller_node_with_stateEstimator src/nodes/position_controller_node_with_stateEstimator.cpp)
add_dependencies(position_controller_node_with_stateEstimator ${catkin_EXPORTED_TARGETS})
target_link_libraries(position_controller_node_with_stateEstimator
  position_controller complementary_filter_crazyflie2 crazyflie_onboard_controller sensfusion6 ${catkin_LIBRARIES})

add_executable(position_controller_node_without_stateEstimator src/nodes/position_controller_node_without_stateEstimator.cpp)
add_dependencies(position_controller_node_without_stateEstimator ${catkin_EXPORTED_TARGETS})
target_link_libraries(position_controller_node_without_stateEstimator
  position_controller complementary_filter_crazyflie2  crazyflie_onboard_controller sensfusion6 ${catkin_LIBRARIES})

add_executable(roll_pitch_yawrate_thrust_controller_node
  src/nodes/roll_pitch_yawrate_thrust_controller_node.cpp)
add_dependencies(roll_pitch_yawrate_thrust_controller_node ${catkin_EXPORTED_TARGETS})
target_link_libraries(roll_pitch_yawrate_thrust_controller_node
  roll_pitch_yawrate_thrust_controller ${catkin_LIBRARIES})

install(TARGETS lee_position_controller position_controller crazyflie_onboard_controller roll_pitch_yawrate_thrust_controller
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)

install(TARGETS lee_position_controller_node position_controller_node_with_stateEstimator position_controller_node_without_stateEstimator roll_pitch_yawrate_thrust_controller_node
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(
  DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
)
