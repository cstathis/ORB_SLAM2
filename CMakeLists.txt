cmake_minimum_required(VERSION 2.8)
project(orbslam2)


# First build deps !
ADD_SUBDIRECTORY(Thirdparty/DBoW2)
ADD_SUBDIRECTORY(Thirdparty/g2o)



set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS}    -O3 -march=native -Wno-error=unused-parameter -Wno-error=unused-variable ")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}   -O3 -std=c++11 -march=native -Wno-error=unused-parameter -Wno-error=unused-variable ")

find_package(OpenCV 3.0 QUIET)
if(NOT OpenCV_FOUND)
   find_package(OpenCV 2.4.3 QUIET)
   if(NOT OpenCV_FOUND)
      message(FATAL_ERROR "OpenCV > 2.4.3 not found.")
   endif()
endif()

find_package(Eigen3 3.1.0 REQUIRED)
find_package(Pangolin REQUIRED)

include_directories(
${PROJECT_SOURCE_DIR}
${PROJECT_SOURCE_DIR}/include
${EIGEN3_INCLUDE_DIR}
${Pangolin_INCLUDE_DIRS}
${SLAMBENCH_INCLUDE_DIRS}
)


add_library(${PROJECT_NAME} SHARED
src/System.cc
src/Tracking.cc
src/LocalMapping.cc
src/LoopClosing.cc
src/ORBextractor.cc
src/ORBmatcher.cc
src/FrameDrawer.cc
src/Converter.cc
src/MapPoint.cc
src/KeyFrame.cc
src/Map.cc
src/MapDrawer.cc
src/Optimizer.cc
src/PnPsolver.cc
src/Frame.cc
src/KeyFrameDatabase.cc
src/Sim3Solver.cc
src/Initializer.cc
src/Viewer.cc
)

add_library(${PROJECT_NAME}-original SHARED
  orbslam2_library.cpp
  )

target_link_libraries(${PROJECT_NAME}-original
  ${PROJECT_NAME}
  ${SLAMBENCH_LIBRARIES}
  )
target_link_libraries(${PROJECT_NAME}
${OpenCV_LIBS}
${EIGEN3_LIBS}
${Pangolin_LIBRARIES}
DBoW2
g2o
)

# Build examples

generate_slam_library(${PROJECT_NAME}-original "")
