

# Set minimum CMake version
cmake_minimum_required(VERSION 2.8.4)

##### Project Setup #####

# Set our output target
set(TARGET mpu9250)

# Configure project and languages
project(${TARGET} C CXX ASM)

set(CMAKE_C_FLAGS "-std=gnu11 -g")
set(CMAKE_CXX_FLAGS "-std=gnu++11 -g")

# Set build type
set(CMAKE_BUILD_TYPE DEBUG)

##### Modules #####

include(${PROJECT_SOURCE_DIR}/lib/mpu9250.cmake)
#add_subdirectory(${PROJECT_SOURCE_DIR}/modules/googletest/googlemock)

##### Files #####

# Add project headers
include_directories(${PROJECT_SOURCE_DIR}/test/include)
include_directories(${PROJECT_SOURCE_DIR}/util/include)

# Add project sources
set(TEST_SOURCES
    ${PROJECT_SOURCE_DIR}/test/source/main.cpp
    ${PROJECT_SOURCE_DIR}/test/source/mpu9250test.cpp
)

set(UTIL_SOURCES
    ${PROJECT_SOURCE_DIR}/util/source/main.cpp
)

##### Pre Build #####
add_custom_target(version ${PROJECT_SOURCE_DIR}/version.py LIBMPU9250 ${CMAKE_BINARY_DIR}/version.h)
include_directories(${CMAKE_BINARY_DIR})

##### Outputs #####

# Built Tests
add_executable(${TARGET}test ${TEST_SOURCES})
target_link_libraries(${TARGET}test ${OPTIONAL_LIBS} pthread gmock)
add_dependencies(${TARGET}test version)

# Build Util (requires libusbthing and USB-Thing hardware).
if(BUILD_UTIL)
add_executable(${TARGET}util ${UTIL_SOURCES})
target_link_libraries(${TARGET}util ${OPTIONAL_LIBS} usbthing pthread)
add_dependencies(${TARGET}util version)
endif()

##### Testing #####
add_custom_target(tests COMMAND ${TARGET}test)
