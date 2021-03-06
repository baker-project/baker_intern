cmake_minimum_required(VERSION 2.8.3)
project(scitos_mira)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS geometry_msgs roscpp std_msgs sensor_msgs tf nav_msgs scitos_msgs dynamic_reconfigure move_base_msgs actionlib actionlib_msgs eigen_conversions nav_msgs)


## System dependencies are found with CMake's conventions
find_package(Boost REQUIRED COMPONENTS thread)


## Uncomment this if the package has a setup.py. This macro ensures
## modules and global scripts declared therein get installed
## See http://ros.org/doc/groovy/api/catkin/html/user_guide/setup_dot_py.html
# catkin_python_setup()

#######################################
## Declare ROS messages and services ##
#######################################

## Generate messages in the 'msg' folder
# add_message_files(
#   FILES
#   Message1.msg
#   Message2.msg
# )

## Generate services in the 'srv' folder
#add_service_files(
#   FILES
#   ResetMotorStop.srv
#
#)

## Generate added messages and services with any dependencies listed here
# generate_messages(
#   DEPENDENCIES
#   geometry_msgs#   std_msgs
# )

#######################
# DYNAMIC RECONFIGURE
#######################
#find_package(catkin REQUIRED dynamic_reconfigure)
generate_dynamic_reconfigure_options(
  cfg/ChargerParameters.cfg
  cfg/EBCParameters.cfg
  cfg/DisplayParameters.cfg
)

#add dynamic_reconfigure header directory
include_directories(cfg/cpp)

###################################
## catkin specific configuration ##
###################################
## The catkin_package macro generates cmake config files for your package
## Declare things to be passed to dependent projects
## LIBRARIES: libraries you create in this project that dependent projects also need
## CATKIN_DEPENDS: catkin_packages dependent projects also need
## DEPENDS: system dependencies of this project that dependent projects also need
catkin_package(
   INCLUDE_DIRS include
#  LIBRARIES scitos
#  CATKIN_DEPENDS geometry_msgs roscpp std_msgs
#  DEPENDS system_lib
)

###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(include
  ${catkin_INCLUDE_DIRS}
)

## Declare a cpp library
# add_library(scitos
#   src/${PROJECT_NAME}/scitos.cpp
# )

# include cmake file for MIRA
include(CMakeLists.mira)
MIRA_REQUIRE_PACKAGE(SCITOS) # 0.23.1)

MIRA_FIND_PACKAGE(Pilot)
message("-- PACKAGE_Pilot_FOUND = ${PACKAGE_Pilot_FOUND}")

## Declare a cpp executable
#add_executable(scitos_node src/scitos_node.cpp)
add_executable(scitos_node  src/scitos_node.cpp 
							src/ScitosModule.cpp  
							src/ModuleFactory.cpp 
							src/ScitosG5.cpp  
							src/ScitosCharger.cpp
							src/ScitosDisplay.cpp
							src/ScitosDrive.cpp
							src/ScitosEBC.cpp
							src/ScitosHead.cpp)
# make sure configure headers are build before node using them
add_dependencies(scitos_node ${PROJECT_NAME}_gencfg scitos_msgs_gencpp) 
if(${PACKAGE_Pilot_FOUND})
	set_target_properties(scitos_node PROPERTIES COMPILE_DEFINITIONS "__WITH_PILOT__")
endif(${PACKAGE_Pilot_FOUND})

target_link_libraries(scitos_node ${catkin_LIBRARIES})


#############
## Install ##
#############

# all install targets should use catkin DESTINATION variables
# See http://ros.org/doc/groovy/api/catkin/html/adv_user_guide/variables.html

## Mark executable scripts (Python etc.) for installation
## in contrast to setup.py, you can choose the destination
# install(PROGRAMS
#   scripts/my_python_script
#   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark executables and/or libraries for installation
install(TARGETS scitos_node
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

## Mark cpp header files for installation
install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
  PATTERN ".svn" EXCLUDE
)

## Mark launch files for installation
install(DIRECTORY launch/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/launch
  FILES_MATCHING PATTERN "*.launch"
)

## Mark resources files for installation
install(DIRECTORY resources/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/resources
  FILES_MATCHING PATTERN "*"
)


## Mark other files for installation (e.g. launch and bag files, etc.)
# install(FILES
#   # myfile1
#   # myfile2
#   DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
# )

#############
## Testing ##
#############

## Add gtest based cpp test target and link libraries
# catkin_add_gtest(${PROJECT_NAME}-test test/test_scitos.cpp)
# if(TARGET ${PROJECT_NAME}-test)
#   target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
# endif()

## Add folders to be run by python nosetests
# catkin_add_nosetests(test)


##########
## MIRA ##
##########

# for mira support (auto linking, enabling C++0x support,...)
catkin_add_mira(scitos_node)
