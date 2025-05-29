# http://ros.org/doc/groovy/api/catkin/html/user_guide/supposed.html
cmake_minimum_required(VERSION 3.0.2)
project(robotiq_modbus_rtu)
find_package(catkin REQUIRED COMPONENTS rospy)
catkin_python_setup()
catkin_package(
    CATKIN_DEPENDS rospy
)
install(DIRECTORY src/robotiq_modbus_rtu/robotiqmodbus
  DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}/python3/dist-packages/robotiq_modbus_rtu
  FILES_MATCHING PATTERN "*.py"
)
