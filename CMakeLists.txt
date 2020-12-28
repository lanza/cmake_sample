cmake_minimum_required(VERSION 3.17)

project(test CXX)

add_executable(FirstTool test.cpp)
install(TARGETS FirstTool)

if(NOT SECOND_BUILD)
  include(ExternalProject)
  ExternalProject_Add(
    HostBuild
    SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}
    INSTALL_DIR ${CMAKE_CURRENT_BINARY_DIR}/HostTools
    CMAKE_ARGS -DCMAKE_TOOLCHAIN_FILE=Toolchain-Darwin-x86_64.cmake
               -DSECOND_BUILD=TRUE
               -DCMAKE_INSTALL_PREFIX=${CMAKE_CURRENT_BINARY_DIR}/HostTools/)
endif()

if(NOT SECOND_BUILD)
  add_custom_command(
    OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/result
    COMMAND ${CMAKE_CURRENT_BINARY_DIR}/HostTools/bin/FirstTool >
            ${CMAKE_CURRENT_BINARY_DIR}/result
    DEPENDS HostBuild)

  add_custom_target(
    Result
    ALL
    DEPENDS ${CMAKE_CURRENT_BINARY_DIR}/result)
endif()
