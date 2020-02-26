PROJECT(indi-eqmod C CXX)
cmake_minimum_required(VERSION 2.8)

LIST(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake_modules/")
LIST(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/../cmake_modules/")
include(GNUInstallDirs)

set(BIN_INSTALL_DIR "${CMAKE_INSTALL_PREFIX}/bin")

find_package(INDI REQUIRED)
find_package(Nova REQUIRED)
find_package(ZLIB REQUIRED)
find_package(GSL REQUIRED)

set(SITECH_VERSION_MAJOR 0)
set(SITECH_VERSION_MINOR 1)

if (CYGWIN)
add_definitions(-U__STRICT_ANSI__)
endif(CYGWIN)

option(WITH_ALIGN "Enable Alignment Subsystem" ON)
option(WITH_ALIGN_GEEHALEL "Enable EQMod Alignment" ON)
option(WITH_SCOPE_LIMITS "Enable Scope limits" ON)

set(INDI_DATA_DIR "${CMAKE_INSTALL_PREFIX}/share/indi")
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/config.h.cmake ${CMAKE_CURRENT_BINARY_DIR}/config.h )
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/indi_sitech.xml.cmake ${CMAKE_CURRENT_BINARY_DIR}/indi_sitech.xml )

include_directories( ${CMAKE_CURRENT_BINARY_DIR})
include_directories( ${CMAKE_CURRENT_SOURCE_DIR})
include_directories( ${INDI_INCLUDE_DIR})
include_directories( ${NOVA_INCLUDE_DIR})
include_directories( ${NOVA_INCLUDE_DIR}/..)
include_directories( ${GSL_INCLUDE_DIR} )

include(CMakeCommon)

IF ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU" AND CMAKE_CXX_COMPILER_VERSION VERSION_GREATER 6.9.9)
    SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-format-overflow")
ENDIF ()

########### EQMod ###############
set(sitech_CXX_SRCS
   ${CMAKE_CURRENT_SOURCE_DIR}/telescope_sitech.cpp)

if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
  set(sitech_CXX_SRCS ${sitech_CXX_SRCS}
    ${CMAKE_CURRENT_SOURCE_DIR}/mach_gettime.cpp)
endif()

add_executable(indi_sitech_telescope ${eqmod_C_SRCS} ${sitech_CXX_SRCS})

target_link_libraries(indi_sitech_telescope ${INDI_LIBRARIES} nova)

if (CMAKE_SYSTEM_PROCESSOR MATCHES "arm*")
target_link_libraries(indi_sitech_telescope rt)
endif (CMAKE_SYSTEM_PROCESSOR MATCHES "arm*")

install(TARGETS indi_sitech_telescope RUNTIME DESTINATION bin )

install( FILES  ${CMAKE_CURRENT_BINARY_DIR}/indi_sitech.xml DESTINATION ${INDI_DATA_DIR})

###################################################################################################
#########################################  Tests  #################################################
###################################################################################################

find_package (GTest)
find_package (GMock)
IF (GTEST_FOUND)
  IF (INDI_BUILD_UNITTESTS)
    MESSAGE (STATUS  "Building unit tests")
    ADD_SUBDIRECTORY(test)
  ELSE (INDI_BUILD_UNITTESTS)
    MESSAGE (STATUS  "Not building unit tests")
  ENDIF (INDI_BUILD_UNITTESTS)
ELSE()
  MESSAGE (STATUS  "GTEST not found, not building unit tests")
ENDIF (GTEST_FOUND)
