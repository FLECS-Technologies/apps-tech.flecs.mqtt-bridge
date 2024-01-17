# Copyright 2021-2023 FLECS Technologies GmbH
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

cmake_minimum_required(VERSION 3.21)

include(${CMAKE_SOURCE_DIR}/flecs-build/cmake/flecs-build.cmake)

project(tech.flecs.mqtt-bridge VERSION 3.0.0)

if("${CMAKE_SOURCE_DIR}" STREQUAL "${CMAKE_CURRENT_SOURCE_DIR}")
    add_subdirectory(flecs-external)
endif()
add_subdirectory(flunder)

if(FLECS_BUILD_TESTS)
    include(CTest)
    add_compile_options(-fprofile-arcs -ftest-coverage)
    add_link_options(-lgcov --coverage)
    add_definitions(-DFLECS_UNIT_TEST)
    add_definitions(-DFLECS_CONSTEXPR_UNLESS_TESTED=)
    add_definitions(-DFLECS_FINAL_UNLESS_TESTED=)
else()
    add_definitions(-DFLECS_CONSTEXPR_UNLESS_TESTED=constexpr)
    add_definitions(-DFLECS_FINAL_UNLESS_TESTED=final)
endif()

set(SRC
    src/mqtt_bridge.cpp
)

set(HEADER
    include/mqtt_bridge/mqtt_bridge.h
)

add_executable(mqtt_bridge ${SRC} ${HEADER})

target_include_directories(mqtt_bridge PRIVATE include)
target_link_libraries(mqtt_bridge PUBLIC
    flunder.shared
    PkgConfig::Mosquitto
)

install(TARGETS mqtt_bridge RUNTIME)
install(TARGETS flunder.shared LIBRARY)
get_target_property(zenohc-lib zenohc::lib IMPORTED_LOCATION)
install(
    FILES ${zenohc-lib}
    DESTINATION ${CMAKE_INSTALL_LIBDIR}
    PERMISSIONS OWNER_READ OWNER_WRITE OWNER_EXECUTE
    PERMISSIONS GROUP_READ GROUP_EXECUTE
    PERMISSIONS WORLD_READ WORLD_EXECUTE
)
install(
    CODE "file (INSTALL ${pkgcfg_lib_Mosquitto_mosquitto} DESTINATION ${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR} FOLLOW_SYMLINK_CHAIN)"
)
