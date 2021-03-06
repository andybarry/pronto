set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x -Wno-deprecated-declarations")

cmake_minimum_required(VERSION 2.8 FATAL_ERROR)
project(leg_estimate)


################
set(REQUIRED_PACKAGES  kdl-parser forward-kinematics)

add_library(leg_estimate  SHARED leg_estimate.cpp foot_contact_classify.cpp)
pods_use_pkg_config_packages(leg_estimate estimate_tools  filter_tools foot_contact 
  foot_contact_alt
  ${REQUIRED_PACKAGES} 
  lcmtypes_bot2-core 
  pronto_vis 
  eigen3)
# lcmtypes_fovis-bot2 
set_target_properties(leg_estimate PROPERTIES SOVERSION 1)
pods_install_libraries(leg_estimate)
pods_install_headers( leg_estimate.hpp foot_contact_classify.hpp common_conversions.hpp
               DESTINATION leg_estimate)
pods_install_pkg_config_file(leg_estimate
  LIBS -lleg_estimate  -lfilter_tools -lkalman_filter_tools
  VERSION 0.0.1)


################
add_executable(se-leg-odometry main.cpp LegOdoWrapper.cpp)
target_link_libraries(se-leg-odometry boost_system)
pods_use_pkg_config_packages(se-leg-odometry leg_estimate path-util
   bot2-param-client model-client  
   pronto_math bot2-frames
   )
#bot2-frames_cpp
target_link_libraries (se-leg-odometry lcm )
pods_install_executables(se-leg-odometry )




################
add_executable(se-convert-bdi-into-vicon-frame convert_bdi_into_vicon_frame.cpp )
target_link_libraries(se-convert-bdi-into-vicon-frame boost_system)
pods_use_pkg_config_packages(se-convert-bdi-into-vicon-frame  
    bot2-frames pronto_vis
    path-util ${REQUIRED_PACKAGES})
target_link_libraries (se-convert-bdi-into-vicon-frame lcm  )
pods_install_executables(se-convert-bdi-into-vicon-frame )






################
add_executable(se-init-from-bdi init_from_bdi.cpp )
target_link_libraries(se-init-from-bdi boost_system)
pods_use_pkg_config_packages(se-init-from-bdi 
    path-util ${REQUIRED_PACKAGES}  eigen3
    bot2-frames)
target_link_libraries (se-init-from-bdi lcm   )
pods_install_executables(se-init-from-bdi )
