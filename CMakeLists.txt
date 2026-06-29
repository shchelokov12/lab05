cmake_minimum_required(VERSION 3.10)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_EXAMPLES "Build examples" OFF)
option(BUILD_TESTS "Build tests" ON)

project(print)

add_library(print STATIC sources/print.cpp)

target_include_directories(print PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

if(BUILD_EXAMPLES)
  file(GLOB EXAMPLE_SOURCES "examples/*.cpp")
  foreach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
    get_filename_component(EXAMPLE_NAME ${EXAMPLE_SOURCE} NAME_WE)
    add_executable(${EXAMPLE_NAME} ${EXAMPLE_SOURCE})
    target_link_libraries(${EXAMPLE_NAME} print)
    install(TARGETS ${EXAMPLE_NAME} RUNTIME DESTINATION bin)
  endforeach()
endif()

install(TARGETS print
  EXPORT print-config
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
)

install(DIRECTORY include/ DESTINATION include)
install(EXPORT print-config DESTINATION cmake)

if(BUILD_TESTS)
    enable_testing()
    add_subdirectory(third-party/gtest)

    set(TEST_SOURCES tests/test1.cpp)

    add_executable(check ${TEST_SOURCES})
    target_link_libraries(check print gtest_main pthread)
    add_test(NAME check COMMAND check)
endif()
