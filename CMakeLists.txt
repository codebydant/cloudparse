cmake_minimum_required(VERSION 3.10)

set(CMAKE_INSTALL_PREFIX "${CMAKE_BINARY_DIR}/install" CACHE STRING "Final install location")
set(CMAKE_BUILD_TYPE_INIT "Release")

project(cloudparse
    VERSION 0.2.1
    DESCRIPTION "Cloud parse library for PCL"
    LANGUAGES CXX
)

option(BUILD_TESTING "Builds only the test executable." OFF)
option(CODE_COVERAGE "Collect coverage from test library" OFF)

include("${CMAKE_CURRENT_LIST_DIR}/cmake/color.cmake")
include(ExternalProject)

find_package(PCL 1.8 REQUIRED QUIET COMPONENTS io)

if(PCL_FOUND)
    message(STATUS "PCL status:")
    message(STATUS "    version: ${PCL_VERSION}")
    message(STATUS "    directory: ${PCL_DIR}")
else()
    message(FATAL_ERROR "   ERROR: PCL minimum required version 1.8. Not found")
endif()

# #############################################################################
# COMPILE LIBRARY
# #############################################################################
add_library(${PROJECT_NAME} SHARED
    ${CMAKE_CURRENT_LIST_DIR}/src/parser.cpp
)
add_library(${PROJECT_NAME}::${PROJECT_NAME} ALIAS ${PROJECT_NAME})

# #############################################################################
# COMPILE UNITTESTS
# #############################################################################
if(BUILD_TESTING)
    message("${BoldWhite} Testing enabled ${ColourReset}")
    enable_testing()
    add_subdirectory(external)
    add_subdirectory(tests)

    if(CODE_COVERAGE)
        message("${BoldWhite} Coverage enabled ${ColourReset}")
        include("${CMAKE_CURRENT_LIST_DIR}/cmake/CodeCoverage.cmake")
        append_coverage_compiler_flags()
        setup_target_for_coverage_lcov(NAME coverage EXECUTABLE ctest -j ${n_cores}
            BASE_DIRECTORY ../coverage
            DEPENDENCIES
            testlib
            testconcreteparses
        )
    endif()
endif()

# #############################################################################
# LIBRARY PROPERTIES
# #############################################################################
set_target_properties(${PROJECT_NAME} PROPERTIES
    VERSION ${PROJECT_VERSION}
    SOVERSION 1
    PUBLIC_HEADER ${CMAKE_CURRENT_LIST_DIR}/include/cloudparse/parser.hpp
)

# #############################################################################
# LIBRARY HEADERS
# #############################################################################
target_include_directories(${PROJECT_NAME} PUBLIC
    ${CMAKE_CURRENT_LIST_DIR}/include
    ${PCL_INCLUDE_DIRS}
)

# #############################################################################
# LIBRARY DEPENDENCIES
# #############################################################################
target_link_libraries(${PROJECT_NAME} PRIVATE ${PCL_IO_LIBRARIES})

# #############################################################################
# COMPILATION FLAGS
# #############################################################################
target_compile_options(${PROJECT_NAME} PRIVATE -Wno-cpp -mno-avx -mno-avx2)

# #############################################################################
# INSTALL DIRECTORY
# #############################################################################
install(TARGETS ${PROJECT_NAME}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)
