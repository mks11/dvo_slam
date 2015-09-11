cmake_minimum_required(VERSION 2.8.3)
project(dvo_slam)

# dynamic reconfigure
###rosbuild_find_ros_package(dynamic_reconfigure)
###include(${dynamic_reconfigure_PACKAGE_PATH}/cmake/cfgbuild.cmake)
###gencfg()



# VTK library
find_package(VTK REQUIRED)
include_directories(${VTK_INCLUDE_DIRS})

#Eigen3 Library
find_package(Eigen REQUIRED COMPONENTS Geometry)
include_directories(${Eigen_INCLUDE_DIRS})

#pcl library
find_package(PCL REQUIRED)
include_directories(${PCL_INCLUDE_DIRS})
link_directories(${PCL_LIBRARY_DIRS})
add_definitions(${PCL_DEFINITIONS})

#Find g2O and CPARSE 
SET(CMAKE_MODULE_PATH "~/g2o/cmake_modules/")
find_package(G2O REQUIRED)
find_package(CSparse REQUIRED)
#include_directories(${G2O_INCLUDE_DIR})
include_directories(${CSPARSE_INCLUDE_DIR})
#find_package(CSparse REQUIRED)
#include_directories(${CSPARSE_INCLUDE_DIRS})


include_directories(~/g2o/lib
  ~/g2o
)

# add Sophus
include_directories("${PROJECT_SOURCE_DIR}/src/Sophus/")

find_package(catkin REQUIRED COMPONENTS
nodelet
dvo_core
dvo_ros
roscpp
interactive_markers
#g2o -- gives error
std_msgs
sensor_msgs
geometry_msgs
message_generation
message_runtime
eigen
visualization_msgs
dynamic_reconfigure
tf
tf_conversions

tf2
tf2_ros
tf2_kdl
tf2_msgs
tf2_tools
tf2_geometry_msgs
tf2_bullet

eigen_conversions
cv_bridge
image_transport
#suitesparse
message_filters
)

#set the default path for built executables to the "bin" directory
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
#set the default path for built libraries to the "lib" directory
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)


# messages (must be called before add_library or executable
###rosbuild_genmsg()

#actually generate the language specific messages
add_message_files(FILES
  PoseStampedArray.msg
)

generate_dynamic_reconfigure_options(
  cfg/dvo_slam.cfg
  cfg/graph_visualizer.cfg
)

generate_messages(DEPENDENCIES std_msgs sensor_msgs)


## must be called before add_library or target
catkin_package(
  INCLUDE_DIRS include
  CATKIN_DEPENDS message_runtime
)

include_directories(
  ${PROJECT_SOURCE_DIR}/include
  ${PROJECT_SOURCE_DIR}/../dvo_core/include
  ${PROJECT_SOURCE_DIR}/../dvo_ros/include
 # /usr/lib/libcsparse.so.2.2.3
)


add_library(${PROJECT_NAME} 
  src/keyframe_tracker.cpp
  src/keyframe_graph.cpp
  src/keyframe_constraint_search.cpp
  src/camera_keyframe_tracking.cpp
  
  src/config.cpp
  
  src/tracking_result_evaluation.cpp
  src/local_map.cpp
  src/local_tracker.cpp
  
  src/constraints/constraint_proposal.cpp
  src/constraints/constraint_proposal_voter.cpp
  src/constraints/constraint_proposal_validator.cpp
  
  src/serialization/map_serializer_interface.cpp
  src/serialization/map_serializer.cpp
  
  src/visualization/graph_visualizer.cpp
  
  src/types.cpp
)

MESSAGE(STATUS "g2o:" ${G2O_INCLUDE_DIR})

target_link_libraries(${PROJECT_NAME}
  #g2o_core
  #g2o_solver_dense
  #g2o_solver_csparse
  #g2o_csparse_extension
  #csparse
  #g2o_types_slam3d
  ${catkin_LIBRARIES}
  ~/g2o/lib/libg2o_core.so
  ~/g2o/lib/libg2o_solver_dense.so
  ~/g2o/lib/libg2o_solver_csparse.so
  ~/g2o/lib/libg2o_csparse_extension.so
  ~/g2o/lib/libg2o_types_slam3d.so
  dvo_slam
  dvo_core
  dvo_ros
  dvo_visualization
  # camera_tracker_nodelet
  /usr/local/lib/libopencv_core.so
  ${OpenCV_LIBS}
  ${PCl_COMMON_LIBRARIES}
  ${PCl_IO_LIBRARIES}
  ${PCL_VISUALIZATION_LIBRARIES}
  ${PCL_FILTERS_LIBRARIES}
  ${Boost_LIBS}
  ${tf_LIBS}
  ${Eigen_LIBS}
)

add_executable(camera_keyframe_tracker
  src/camera_keyframe_tracker_node.cpp
)

target_link_libraries(camera_keyframe_tracker
  #${PROJECT_NAME} previously
  ${catkin_LIBRARIES}
  dvo_slam
  dvo_core

)
