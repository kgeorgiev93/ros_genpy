cmake_minimum_required(VERSION 2.8.3)

include(cmake/HunterGate.cmake)
HunterGate(
    URL "https://github.com/ruslo/hunter/archive/v0.23.184.tar.gz"
    SHA1 "fe3fb05d51c21499c7eebbe7d0e102742a54a9cd"
)

project(genpy)

hunter_add_package(catkin)
hunter_add_package(ros_genmsg)

find_package(catkin REQUIRED COMPONENTS genmsg)

catkin_package(
  CATKIN_DEPENDS genmsg
  CFG_EXTRAS genpy-extras.cmake
)

add_subdirectory(scripts)

file(WRITE ${CATKIN_DEVEL_PREFIX}/${GENMSG_LANGS_DESTINATION}/genpy "Python")
install(FILES ${CATKIN_DEVEL_PREFIX}/${GENMSG_LANGS_DESTINATION}/genpy
  DESTINATION ${GENMSG_LANGS_DESTINATION})

catkin_python_setup()

if(CATKIN_ENABLE_TESTING)
  assert(CATKIN_ENV)
  add_custom_target(generate_test_messages
    COMMAND
    "${CATKIN_ENV}" "${PYTHON_EXECUTABLE}"
    "${CMAKE_CURRENT_SOURCE_DIR}/test/msg/generate_test_messages.py")
  if(TARGET tests)
    add_dependencies(tests generate_test_messages)
  endif()

  catkin_add_nosetests(test DEPENDENCIES generate_test_messages)
endif()
