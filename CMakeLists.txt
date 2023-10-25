cmake_minimum_required(VERSION 3.5)
project(openslam_gmapping)

set(CMAKE_CXX_STANDARD 14)

find_package(ament_cmake REQUIRED)

include(GenerateExportHeader)
set(EXPORT_HEADER_DIR "include")

###################################
## catkin specific configuration ##
###################################
## The catkin_package macro generates cmake config files for your package
## Declare things to be passed to dependent projects

###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
#include_directories(
#  include
#)

#SUBDIRS=utils sensor log configfile scanmatcher gridfastslam gui

# utils/
# OBJS= stat.o  movement.o
# APPS= autoptr_test #stat_test
# CPPFLAGS+= -DFSLINE
add_library(utils SHARED
  utils/stat.cpp
  utils/movement.cpp)
target_include_directories(utils PUBLIC
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/include>"
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
  "$<INSTALL_INTERFACE:include/${PROJECT_NAME}>"
  "$<INSTALL_INTERFACE:include/>")
add_executable(autoptr_test
  utils/autoptr_test.cpp)
target_include_directories(autoptr_test PUBLIC
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
  "$<INSTALL_INTERFACE:include/${PROJECT_NAME}>"
)

# sensor/
# SUBDIRS=sensor_base sensor_odometry sensor_range

# sensor/sensor_base/
# OBJS= sensor.o sensorreading.o
add_library(sensor_base SHARED
  sensor/sensor_base/sensor.cpp
  sensor/sensor_base/sensorreading.cpp)

target_include_directories(sensor_base PUBLIC
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/include>"
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
  "$<INSTALL_INTERFACE:include/${PROJECT_NAME}>"
  "$<INSTALL_INTERFACE:include/>")
# sensor/sensor_odometry/
# LDFLAGS+= -lsensor_base
# OBJS= odometrysensor.o odometryreading.o
add_library(sensor_odometry SHARED
  sensor/sensor_odometry/odometrysensor.cpp
  sensor/sensor_odometry/odometryreading.cpp)
target_include_directories(sensor_odometry PUBLIC
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/include>"
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
  "$<INSTALL_INTERFACE:include/${PROJECT_NAME}>"
  "$<INSTALL_INTERFACE:include/>")
target_link_libraries(sensor_odometry
  sensor_base)

# sensor/sensor_range/
# CPPFLAGS+= -I../
# LDFLAGS+= -lsensor_base
# OBJS= rangesensor.o rangereading.o 
add_library(sensor_range SHARED
  sensor/sensor_range/rangesensor.cpp
  sensor/sensor_range/rangereading.cpp)
target_include_directories(sensor_range PUBLIC
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/include>"
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
  "$<INSTALL_INTERFACE:include/${PROJECT_NAME}>"
  "$<INSTALL_INTERFACE:include/>")
target_link_libraries(sensor_range
  sensor_base)

# log/
# CPPFLAGS+= -I../sensor
# OBJS= configuration.o carmenconfiguration.o sensorlog.o sensorstream.o
# APPS= log_test log_plot scanstudio2carmen rdk2carmen
# LDFLAGS+=  -lsensor_range -lsensor_odometry -lsensor_base 
add_library(log SHARED
  log/configuration.cpp
  log/carmenconfiguration.cpp
  log/sensorlog.cpp
  log/sensorstream.cpp)
target_include_directories(log PUBLIC
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/include>"
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
  "$<INSTALL_INTERFACE:include/${PROJECT_NAME}>"
  "$<INSTALL_INTERFACE:include/>")
add_executable(log_test
  log/log_test.cpp)
add_executable(log_plot
  log/log_plot.cpp)
add_executable(scanstudio2carmen
  log/scanstudio2carmen.cpp)
add_executable(rdk2carmen
  log/rdk2carmen.cpp)
target_link_libraries(log_test log)
target_link_libraries(log_plot log)
target_link_libraries(scanstudio2carmen log)
target_link_libraries(rdk2carmen log)
target_link_libraries(log
  sensor_range sensor_odometry sensor_base)

# configfile/
# OBJS= configfile.o 
# APPS= configfile_test 
add_library(configfile SHARED
  configfile/configfile.cpp)
target_include_directories(configfile PUBLIC
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/include>"
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
  "$<INSTALL_INTERFACE:include/${PROJECT_NAME}>"
  "$<INSTALL_INTERFACE:include/>")
add_executable(configfile_test
  configfile/configfile_test.cpp)
target_link_libraries(configfile_test configfile)

# scanmatcher/
# CPPFLAGS+=-I../sensor
# OBJS= smmap.o scanmatcher.o scanmatcherprocessor.o eig3.o
# APPS= scanmatch_test icptest
# LDFLAGS+= -llog -lsensor_range -lsensor_odometry -lsensor_base -lutils
add_library(scanmatcher SHARED
  scanmatcher/smmap.cpp
  scanmatcher/scanmatcher.cpp
  scanmatcher/scanmatcherprocessor.cpp
  scanmatcher/eig3.cpp)
target_include_directories(scanmatcher PUBLIC
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/include>"
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
  "$<INSTALL_INTERFACE:include/${PROJECT_NAME}>"
  "$<INSTALL_INTERFACE:include/>")
add_executable(scanmatch_test
  scanmatcher/scanmatch_test.cpp)
add_executable(icptest
  scanmatcher/icptest.cpp)
target_link_libraries(scanmatch_test scanmatcher)
target_link_libraries(icptest scanmatcher)
target_link_libraries(scanmatcher
  log sensor_range sensor_odometry sensor_base utils)

# gridfastslam/
# CPPFLAGS+=-I../sensor
# OBJS= gridslamprocessor_tree.o motionmodel.o gridslamprocessor.o gfsreader.o
# APPS= gfs2log gfs2rec gfs2neff #gfs2stat
# LDFLAGS+=  -lscanmatcher -llog -lsensor_range -lsensor_odometry -lsensor_base -lutils
add_library(gridfastslam SHARED
  gridfastslam/gridslamprocessor_tree.cpp
  gridfastslam/motionmodel.cpp
  gridfastslam/gridslamprocessor.cpp
  gridfastslam/gfsreader.cpp)
target_include_directories(gridfastslam PUBLIC
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/include>"
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
  "$<INSTALL_INTERFACE:include/${PROJECT_NAME}>"
  "$<INSTALL_INTERFACE:include/>")
add_executable(gfs2log
  gridfastslam/gfs2log.cpp)
add_executable(gfs2rec
  gridfastslam/gfs2rec.cpp)
add_executable(gfs2neff
  gridfastslam/gfs2neff.cpp)
target_link_libraries(gfs2log gridfastslam)
target_link_libraries(gfs2rec gridfastslam)
target_link_libraries(gfs2neff gridfastslam)
target_link_libraries(gridfastslam
  scanmatcher log sensor_range sensor_odometry sensor_base utils)

#############
## Install ##
#############

# all install targets should use catkin DESTINATION variables
# See http://ros.org/doc/api/catkin/html/adv_user_guide/variables.html

## Mark executable scripts (Python etc.) for installation
## in contrast to setup.py, you can choose the destination
# install(PROGRAMS
#   scripts/my_python_script
#   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark executables and/or libraries for installation
install(TARGETS utils sensor_base sensor_odometry sensor_range log configfile scanmatcher gridfastslam
  EXPORT export_${PROJECT_NAME}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)

install(TARGETS autoptr_test log_test log_plot scanstudio2carmen rdk2carmen configfile_test scanmatch_test icptest gfs2log gfs2rec gfs2neff
  RUNTIME DESTINATION lib/${PROJECT_NAME}
)

## Mark cpp header files for installation
install(DIRECTORY include/gmapping
  DESTINATION include
  FILES_MATCHING PATTERN "*.h*"
  PATTERN ".svn" EXCLUDE
)

generate_export_header(utils
  EXPORT_FILE_NAME ${EXPORT_HEADER_DIR}/gmapping/utils/utils_export.h)
generate_export_header(sensor_base
  EXPORT_FILE_NAME ${EXPORT_HEADER_DIR}/gmapping/sensor/sensor_base/sensor_base_export.h)
generate_export_header(sensor_odometry
  EXPORT_FILE_NAME ${EXPORT_HEADER_DIR}/gmapping/sensor/sensor_odometry/sensor_odometry_export.h)
generate_export_header(sensor_range
  EXPORT_FILE_NAME ${EXPORT_HEADER_DIR}/gmapping/sensor/sensor_range/sensor_range_export.h)
generate_export_header(log
  EXPORT_FILE_NAME ${EXPORT_HEADER_DIR}/gmapping/log/log_export.h)
generate_export_header(configfile
  EXPORT_FILE_NAME ${EXPORT_HEADER_DIR}/gmapping/configfile/configfile_export.h)
generate_export_header(scanmatcher
  EXPORT_FILE_NAME ${EXPORT_HEADER_DIR}/gmapping/scanmatcher/scanmatcher_export.h)
generate_export_header(gridfastslam
  EXPORT_FILE_NAME ${EXPORT_HEADER_DIR}/gmapping/gridfastslam/gridfastslam_export.h)

install(DIRECTORY
  include/
  DESTINATION include
)


install(FILES
   ${CMAKE_CURRENT_BINARY_DIR}/include/gmapping/utils/utils_export.h
    DESTINATION ${CMAKE_INSTALL_PREFIX}/include/gmapping/utils/
)

install(FILES
   ${CMAKE_CURRENT_BINARY_DIR}/include/gmapping/sensor/sensor_base/sensor_base_export.h
    DESTINATION ${CMAKE_INSTALL_PREFIX}/include/gmapping/sensor/sensor_base/
)

install(FILES
   ${CMAKE_CURRENT_BINARY_DIR}/include/gmapping/sensor/sensor_odometry/sensor_odometry_export.h
    DESTINATION ${CMAKE_INSTALL_PREFIX}/include/gmapping/sensor/sensor_odometry/
)

install(FILES
   ${CMAKE_CURRENT_BINARY_DIR}/include/gmapping/sensor/sensor_range/sensor_range_export.h
    DESTINATION ${CMAKE_INSTALL_PREFIX}/include/gmapping/sensor/sensor_range/
)

install(FILES
   ${CMAKE_CURRENT_BINARY_DIR}/include/gmapping/log/log_export.h
    DESTINATION ${CMAKE_INSTALL_PREFIX}/include/gmapping/log
)

install(FILES
   ${CMAKE_CURRENT_BINARY_DIR}/include/gmapping/configfile/configfile_export.h
    DESTINATION ${CMAKE_INSTALL_PREFIX}/include/gmapping/configfile
)
install(FILES
   ${CMAKE_CURRENT_BINARY_DIR}/include/gmapping/scanmatcher/scanmatcher_export.h
    DESTINATION ${CMAKE_INSTALL_PREFIX}/include/gmapping/scanmatcher
)

install(FILES
   ${CMAKE_CURRENT_BINARY_DIR}/include/gmapping/gridfastslam/gridfastslam_export.h
    DESTINATION ${CMAKE_INSTALL_PREFIX}/include/gmapping/gridfastslam
)

ament_export_include_directories(
    include
)
ament_export_libraries(
    utils
    sensor_base
    sensor_odometry
    sensor_range
    log
    configfile
    scanmatcher
    gridfastslam
)
ament_export_targets(export_${PROJECT_NAME} HAS_LIBRARY_TARGET)
ament_package()
