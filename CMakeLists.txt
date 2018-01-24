cmake_minimum_required(VERSION 3.0)
project(Schroedinger)

if("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
    # require at least gcc 4.8
    if (CMAKE_CXX_COMPILER_VERSION VERSION_LESS 6.0)
        message(FATAL_ERROR "GCC version must be at least 6.0!")
    endif()
elseif ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "clang")
    # require at least clang 3.2
    message(FATAL_ERROR "Clang not supported!")
else()
    message(FATAL_ERROR "You are using an unsupported compiler! Compilation has only been tested with GCC.")
endif()


find_package(Threads REQUIRED)
# Add all sources file
file(GLOB_RECURSE SOURCES src/*.cpp)
include_directories (include)

set(GOOGLETEST_ROOT external/googletest/googletest)

include_directories(
    ${PROJECT_SOURCE_DIR}/${GOOGLETEST_ROOT}
    ${PROJECT_SOURCE_DIR}/${GOOGLETEST_ROOT}/include
)

set(GOOGLETEST_SOURCES
    ${PROJECT_SOURCE_DIR}/${GOOGLETEST_ROOT}/src/gtest-all.cc
    ${PROJECT_SOURCE_DIR}/${GOOGLETEST_ROOT}/src/gtest_main.cc
)

foreach(_source ${GOOGLETEST_SOURCES})
    set_source_files_properties(${_source} PROPERTIES GENERATED 1)
endforeach()

add_library(googletest ${GOOGLETEST_SOURCES})

add_executable (Schroedinger ${SOURCES})
add_executable(
    unit_tests
    tests/main.cpp
)
add_dependencies(unit_tests googletest)

target_link_libraries(
    unit_tests
    googletest
    pthread
)

include(CTest)
enable_testing()

set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR}/bin)