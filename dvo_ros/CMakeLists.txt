cmake_minimum_required(VERSION 2.8.3)
project(dvo_ros)


#set the default path for built executables to the "bin" directory
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
#set the default path for built libraries to the "lib" directory
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)


#common commands for building c++ executables and libraries
#rosbuild_add_library(${PROJECT_NAME} src/example.cpp)
#target_link_libraries(${PROJECT_NAME} another_library)
#rosbuild_add_boost_directories()
#rosbuild_link_boost(${PROJECT_NAME} thread)
#rosbuild_add_executable(example examples/example.cpp)
#target_link_libraries(example ${PROJECT_NAME})

# Eigen3 library
find_package(Eigen REQUIRED)
include_directories(${Eigen_INCLUDE_DIRS})

##
find_package(catkin REQUIRED COMPONENTS 
dynamic_reconfigure 
roscpp 
dvo_core 
nodelet
interactive_markers
visualization_msgs
cv_bridge
tf
tf_conversions
eigen
eigen_conversions
image_transport
geometry_msgs
message_generation
message_runtime

#tf2
#tf2_ros
#tf2_kdl
#tf2_msgs
#tf2_tools
#tf2_geometry_msgs
#tf2_bullet
)

# dynamic reconfigure
###include(${dynamic_reconfigure_PACKAGE_PATH}/cmake/cfgbuild.cmake)

#pcl library
find_package(PCL REQUIRED)
include_directories(${PCL_INCLUDE_DIRS})
link_directories(${PCL_LIBRARY_DIRS})
add_definitions(${PCL_DEFINITIONS})


# opencv
find_package(OpenCV REQUIRED)
include_directories(${OpenCV_INCLUDE_DIRS})


###gencfg()
generate_dynamic_reconfigure_options(
cfg/dvo.cfg
)

catkin_package(CATKIN_DEPENDS roscpp tf
  DEPENDS Boost tf 
  INCLUDE_DIRS include
)

##
include_directories(${PROJECT_SOURCE_DIR}/../dvo_core/include
  ${PROJECT_SOURCE_DIR}/include)

##MESSAGE(STATUS "project src dir: " dvo_core/include)

add_library(${PROJECT_NAME} 
    src/camera_base.cpp
    src/camera_dense_tracking.cpp
    
    src/visualization/ros_camera_trajectory_visualizer.cpp
)

add_library(camera_tracker_nodelet
  src/camera_tracker_nodelet.cpp
)

target_link_libraries(camera_tracker_nodelet
    #${PROJECT_NAME}
    ${catkin_LIBRARIES}
    
)

add_executable(camera_tracker
     src/camera_tracker_node.cpp
)

###############  For debug purpose #####################

##MESSAGE(STATUS "pcl..libs: " ${PCL_VISUALIZATION_LIBRARIES})
##MESSAGE(STATUS "opencv ..." ${OpenCV_LIBS})
##MESSAGE(STATUS "opencv .." ${OpenCV_INCLUDE_DIRS})

########################################################
target_link_libraries(camera_tracker
  ${PROJECT_NAME}
  ${catkin_LIBRARIES}
  dvo_core
  dvo_ros
  dvo_visualization
  # camera_tracker_nodelet
    opencv_core
  ${OpenCV_LIBS}
  ${PCl_COMMON_LIBRARIES}
  ${PCl_IO_LIBRARIES}
  ${PCL_VISUALIZATION_LIBRARIES}
  ${PCL_FILTERS_LIBRARIES}
  ${Boost_LIBS}
  # ${tf_LIBS}
)

#MESSAGE(STATUS "tf-libs" ${tf_LIBS})