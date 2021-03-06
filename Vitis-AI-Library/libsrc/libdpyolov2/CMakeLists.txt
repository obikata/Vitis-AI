#
# Copyright 2019 Xilinx Inc.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
#
cmake_minimum_required(VERSION 3.5)
project(dpyolov2 VERSION 1.0.0 LANGUAGES C CXX)
set(OpenCV_LIBS opencv_core opencv_video opencv_videoio opencv_imgproc opencv_imgcodecs opencv_highgui)
set(Pthread_LIB )
if(CMAKE_SYSTEM_NAME STREQUAL "QNX")
  ## TODO: fix hard coded directory
  link_directories(/home/administrator/workspace/qtbase/lib/)
  list(APPEND OpenCV_LIBS Qt5Widgets Qt5Test Qt5Concurrent Qt5Gui Qt5Core)
else()
  list(APPEND Pthread_LIB pthread)
endif()

list(APPEND CMAKE_MODULE_PATH "${CMAKE_SYSROOT}/usr/share/cmake/")
include_directories("${CMAKE_SOURCE_DIR}/include")

include(XilinxCommon)
include(XilinxVersion)
add_library(${PROJECT_NAME}
  include/xilinx/ai/yolov2.hpp
  src/yolov2.cpp
  src/yolov2_imp.hpp
  src/yolov2_imp.cpp
  src/utils.hpp
  src/utils.cpp
  ${CMAKE_BINARY_DIR}/version.c
  )
set_target_properties(${PROJECT_NAME} PROPERTIES
  VERSION "${PROJECT_VERSION}"
  SOVERSION "${PROJECT_VERSION_MAJOR}")
target_link_libraries(${PROJECT_NAME} xnnpp dpbase dpmath dpproto glog ${OpenCV_LIBS} ${Pthread_LIB})
install(TARGETS ${PROJECT_NAME} DESTINATION lib)
install(FILES include/xilinx/ai/yolov2.hpp DESTINATION include/xilinx/ai)

include_directories("${CMAKE_SOURCE_DIR}/include")
add_executable(test_yolov2 test/test_yolov2.cpp)
target_link_libraries(test_yolov2 ${PROJECT_NAME} ${OpenCV_LIBS})
add_executable(test_accuracy_yolov2 test/test_accuracy_yolov2.cpp)
target_link_libraries(test_accuracy_yolov2 ${PROJECT_NAME} ${OpenCV_LIBS})


