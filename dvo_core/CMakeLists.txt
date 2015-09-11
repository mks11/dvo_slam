cmake_minimum_required(VERSION 2.8.3)
project(dvo_core)
##### include($ENV{ROS_ROOT}/core/rosbuild/rosbuild.cmake)

find_package(catkin REQUIRED COMPONENTS
roscpp
#opencv2 #error cmake no config file 
eigen
#tbb # error cmake no config file
)

#####rosbuild_init()


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

set(CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS} "-msse -msse2 -msse3")
set(CMAKE_CXX_FLAGS_RELWITHDEBINFO
   "${SSE_FLAGS} -O3 -march=native"
)


# Eigen3 library
find_package(Eigen REQUIRED)
include_directories(${Eigen_INCLUDE_DIRS})

# VTK library
find_package(VTK REQUIRED)
include_directories(${VTK_INCLUDE_DIRS})

#PCL library
find_package(PCL REQUIRED)
include_directories(${PCL_INCLUDE_DIRS})

#Sophus library 
#set(sophus_DIR ~/Sophus)
#find_package(sophus REQUIRED)
include_directories("${PROJECT_SOURCE_DIR}/src/Sophus/")
# SSE flags
###rosbuild_check_for_sse()

MESSAGE(STATUS "Project source dir: " ${PROJECT_SOURCE_DIR})

catkin_package(
  INCLUDE_DIRS include 
  LIBRARIES ${PROJECT_NAME} 
)

###########
## Build ##
###########

include_directories(include ${catkin_INCLUDE_DIRS})

### removed rosbuild_add_library to add_library

add_library(dvo_core
    src/core/interpolation.cpp
    src/core/intrinsic_matrix.cpp
    src/core/least_squares.cpp
    src/core/math_sse.cpp
    src/core/rgbd_image.cpp
    src/core/rgbd_image_sse.cpp
    src/core/point_selection.cpp
    src/core/surface_pyramid.cpp
    src/core/weight_calculation.cpp
    
    src/util/histogram.cpp
    
    src/visualization/camera_trajectory_visualizer.cpp

    src/dense_tracking.cpp
    src/dense_tracking_impl.cpp
    src/dense_tracking_config.cpp
)

target_link_libraries(${PROJECT_NAME} 
    tbb
    boost_thread
)

### removed rosbuild_add_library to add_library
add_library(dvo_visualization
    src/visualization/async_point_cloud_builder.cpp
    src/visualization/point_cloud_aggregator.cpp
    src/visualization/pcl_camera_trajetory_visualizer.cpp
)

target_link_libraries(dvo_visualization
  ${PROJECT_NAME} 
  ${catkin_LIBRARIES}
)

#rosbuild_add_executable(sse_test
#    src/sse_test.cpp
#    src/core/math_sse.cpp
#)
