cmake_minimum_required(VERSION 3.0.2)
project(offline_timestamp_align)

add_compile_options(-std=c++11)

find_package(catkin REQUIRED COMPONENTS
  cv_bridge
  geometry_msgs
  image_transport
  message_generation
  roscpp
  sensor_msgs
  std_msgs
  rosbag
)

## 查找系统依赖
find_package(OpenCV REQUIRED)
find_package(Boost REQUIRED COMPONENTS 
  filesystem  
  system      
)

## 消息文件
add_message_files(
  FILES
  FusedState.msg
)

## 生成消息
generate_messages(
  DEPENDENCIES
  std_msgs
  geometry_msgs
  sensor_msgs
)

## catkin 配置
catkin_package(
  CATKIN_DEPENDS 
    cv_bridge 
    roscpp 
    sensor_msgs 
    std_msgs 
    image_transport
  DEPENDS 
    OpenCV 
    Boost
)

###########
## Build ##
###########

include_directories(
  ${catkin_INCLUDE_DIRS}
  ${OpenCV_INCLUDE_DIRS}
  ${Boost_INCLUDE_DIRS}
)

## 可执行文件
add_executable(split src/split.cpp)
add_executable(cam_w_norm_calculate src/cam_w_norm_calculate.cpp)
add_executable(imu_w_norm_calculate src/imu_w_norm_calculate.cpp)
add_executable(time_offset_calculate_portion src/time_offset_calculate_portion.cpp)
add_executable(time_offset_calculate_norm src/time_offset_calculate_norm.cpp)
add_executable(timestamp_correct src/timestamp_correct.cpp)
add_executable(publish_node src/publish_node.cpp)
add_executable(fusion_node src/fusion_node.cpp)

## 保证消息生成在编译前完成
add_dependencies(split ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
add_dependencies(cam_w_norm_calculate ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
add_dependencies(imu_w_norm_calculate ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
add_dependencies(time_offset_calculate_portion ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
add_dependencies(time_offset_calculate_norm ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
add_dependencies(timestamp_correct ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
add_dependencies(publish_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})
add_dependencies(fusion_node ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

## 链接库
target_link_libraries(split
  ${catkin_LIBRARIES}
  ${OpenCV_LIBS}
  ${Boost_LIBRARIES} 
)
target_link_libraries(cam_w_norm_calculate
  ${catkin_LIBRARIES}
  ${OpenCV_LIBS}
)
target_link_libraries(imu_w_norm_calculate
  ${catkin_LIBRARIES}
)
target_link_libraries(time_offset_calculate_portion
  ${catkin_LIBRARIES}
)
target_link_libraries(time_offset_calculate_norm
  ${catkin_LIBRARIES}
)
target_link_libraries(timestamp_correct
  ${catkin_LIBRARIES}
)
target_link_libraries(publish_node
  ${catkin_LIBRARIES}
  ${OpenCV_LIBS}
)
target_link_libraries(fusion_node
  ${catkin_LIBRARIES}
)

#############
## Install ##
#############

## 安装可执行文件
install(TARGETS
  split
  cam_w_norm_calculate
  imu_w_norm_calculate
  time_offset_calculate_portion
  time_offset_calculate_norm
  timestamp_correct
  publish_node
  fusion_node
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)
