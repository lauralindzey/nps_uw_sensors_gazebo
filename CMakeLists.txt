cmake_minimum_required(VERSION 2.8.3)
project(nps_uw_sensors_gazebo)

find_package(catkin REQUIRED tf)

find_package(gazebo REQUIRED)
find_package(OpenCV)

include_directories(
  ${catkin_INCLUDE_DIRS} 
  ${GAZEBO_INCLUDE_DIRS}
)

link_directories(
  ${GAZEBO_LIBRARY_DIRS}
)

list(APPEND CMAKE_CXX_FLAGS "${GAZEBO_CXX_FLAGS}")

catkin_package()

### old gpu_ray ROS component
# old gpu_ray based single beam Gazebo ROS sonar plugin
add_library(nps_gazebo_ros_gpu_ray_single_beam_plugin
            src_gpu_ray/nps_gazebo_ros_gpu_ray_single_beam.h
            src_gpu_ray/nps_gazebo_ros_gpu_ray_single_beam.cpp)
target_link_libraries(nps_gazebo_ros_gpu_ray_single_beam_plugin
                      GpuRayPlugin ${catkin_LIBRARIES})

### nps_beam ROS component
# new nps_beam based Gazebo ROS sonar plugin
add_library(nps_gazebo_ros_beam_plugin
            src_gazebo_beam/NpsBeamSensor.hh
            src_beam/nps_gazebo_ros_beam.h
            src_beam/nps_gazebo_ros_beam.cpp)
target_link_libraries(nps_gazebo_ros_beam_plugin
                      NpsBeamPlugin ${catkin_LIBRARIES})

### nps_beam Gazebo components
add_library(NpsBeamSensor SHARED
            src_gazebo_beam/add_nps_beam_sensor.cc
            src_gazebo_beam/NpsBeamSensor.cc
            src_gazebo_beam/NpsBeamSensorTypes.hh
            src_gazebo_beam/NpsBeamSensor.hh
            src_gazebo_beam/NpsBeamSensorPrivate.hh)
target_link_libraries(NpsBeamSensor ${GAZEBO_LIBRARIES})

add_library(NpsBeamPlugin SHARED src_gazebo_beam/NpsBeamPlugin.cc
                                 src_gazebo_beam/NpsBeamPlugin.hh)
target_link_libraries(NpsBeamPlugin ${GAZEBO_LIBRARIES})

# for launch
install(DIRECTORY launch
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
        PATTERN "*~" EXCLUDE)

