# Top-level CMakeLists
# file for the CMake Best Practices book CMake example project
#
# SPDX-License-Identifier: MIT

# Specifies the minimum required CMake version to build this project (and
# subprojects) Subprojects may override this value by specifying another version
# requirement in their CMakeLists file (e.g. require a later version of CMake)
cmake_minimum_required(VERSION 3.25...3.30)

# The main project
project(
    "CMakeBestPractices"
    VERSION 1.0
    DESCRIPTION
        "The 'build-all' instructions for all examples for the book CMake Best Practices"
    LANGUAGES CXX
)

# TODO(CK) only needed if working as "Dashboard Client" include(CTest)
enable_testing()

# cmake-format: off
# see the CMAKE_SOURCE_DIR like readonly!
if(CMAKE_SOURCE_DIR STREQUAL CMAKE_BINARY_DIR)
    message(
        FATAL_ERROR
            "In-source builds are not supported! "
            "You may need to delete 'CMakeCache.txt' and 'CMakeFiles/' first."
    )
endif()

# Add additional targets to your CMake project:
if(EXISTS ${CMAKE_CURRENT_LIST_DIR}/Format.cmake/CMakeLists.txt)
    add_subdirectory(Format.cmake)
    # check-clang-format check-cmake-format check-format clang-format
    # cmake-format fix-clang-format fix-cmake-format fix-format format
endif()

if(PROJECT_IS_TOP_LEVEL)
    option(BUILD_SHARED_LIBS "Build shared libs." OFF)
    option(BUILD_TESTING "Build SelfTest project" ON)
endif()

# This property setting also needs to be consistent between the installed shared
# library and its consumer, otherwise most toolchains will once again reject the
# consumer's generated BMI.
if(NOT DEFINED CMAKE_CXX_STANDARD)
    set(CMAKE_CXX_STANDARD 20)
    set(CMAKE_CXX_EXTENSIONS FALSE)
    set(CMAKE_CXX_STANDARD_REQUIRED TRUE)

    set(CMAKE_CXX_VISIBILITY_PRESET hidden)
    set(CMAKE_VISIBILITY_INLINES_HIDDEN TRUE)
endif()
# cmake-format: on

# Set the default build type for single-config generators. The build type variable is
# still overridable from outside.
set(CMAKE_BUILD_TYPE "Debug" CACHE STRING
                                   "Default build type for single-config generators"
)

set(CMAKE_EXPORT_COMPILE_COMMANDS TRUE)

# Print generator type and build type (if applicable)
get_property(is_multi_config_generator GLOBAL PROPERTY GENERATOR_IS_MULTI_CONFIG)
if(NOT is_multi_config_generator)
    message(STATUS "Using a single-config generator (${CMAKE_GENERATOR})")
    message(STATUS "Current build type is `${CMAKE_BUILD_TYPE}`")
else()
    message(STATUS "Using a multi-config generator (${CMAKE_GENERATOR})")
endif()

find_program(CCACHE_PROGRAM ccache)
if(CCACHE_PROGRAM)
    set(CMAKE_C_COMPILER_LAUNCHER ${CCACHE_PROGRAM})
    set(CMAKE_CXX_COMPILER_LAUNCHER ${CCACHE_PROGRAM})
endif()

# Add chapter examples to root project
add_subdirectory(chapter01)
add_subdirectory(chapter02)
add_subdirectory(chapter03)
add_subdirectory(chapter04)
add_subdirectory(chapter05)
add_subdirectory(chapter06)
add_subdirectory(chapter07)
add_subdirectory(chapter08)
# the chapter 10 examples are intended to be built standalone
# add_subdirectory(chapter10)

# add chapter 11 examples only on Apple platforms
if(APPLE)
    add_subdirectory(chapter11)
endif()

add_subdirectory(chapter12)
add_subdirectory(chapter13)
add_subdirectory(chapter14)
