cmake_minimum_required(VERSION 2.8.3)
project(dvo_benchmark)

#rosbuild_init()

#set the default path for built executables to the "bin" directory
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
#set the default path for built libraries to the "lib" directory
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)

#uncomment if you have defined messages
#rosbuild_genmsg()
#uncomment if you have defined services
#rosbuild_gensrv()

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

# VTK library
find_package(VTK REQUIRED)
include_directories(${VTK_INCLUDE_DIRS})
link_directories(${VTK_LIBRARY_DIRS})

# opencv
find_package(OpenCV REQUIRED)
include_directories(${OpenCV_INCLUDE_DIRS})

# catkin package
find_package(catkin REQUIRED COMPONENTS
roscpp
dvo_core
dvo_ros
dvo_slam
eigen
pcl_ros
)

#pcl library
find_package(PCL REQUIRED)
include_directories(${PCL_INCLUDE_DIRS})
link_directories(${PCL_LIBRARY_DIRS})
add_definitions(${PCL_DEFINITIONS})

catkin_package(
  INCLUDE_DIRS include 
)

## technically this should have been found in catkin_package
include_directories(
  ${PROJECT_SOURCE_DIR}/../dvo_core/include
  ${PROJECT_SOURCE_DIR}/../dvo_ros/include
  ${PROJECT_SOURCE_DIR}/../dvo_slam/include
  ${PROJECT_SOURCE_DIR}/include
)

add_executable(benchmark
     src/benchmark_slam.cpp
)

#add_executable(experiment
#     src/experiment.cpp
#)


target_link_libraries(benchmark
  #${PROJECT_NAME}
  ## this should technically be not required to add manually 
  ${catkin_LIBRARIES}
  vtkCommon
  dvo_core
  dvo_slam
  dvo_ros
  dvo_visualization
  opencv_core
 
)

# create output directory - just convenience for our users
file(MAKE_DIRECTORY "${PROJECT_SOURCE_DIR}/output")
file(MAKE_DIRECTORY "${PROJECT_SOURCE_DIR}/output/video")
