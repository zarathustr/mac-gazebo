include (${gazebo_cmake_dir}/FindGtest.cmake)

include_directories (
  ${ODE_INCLUDE_DIRS}
  ${OPENGL_INCLUDE_DIR}
  ${OGRE_INCLUDE_DIRS}
  ${SDFormat_INCLUDE_DIRS}
  ${Boost_INCLUDE_DIRS}
  ${GTEST_INCLUDE_DIRS}/gtest/
  ${GTEST_SRC_DIR}
)

link_directories(
  ${CCD_LIBRARY_DIRS}
  ${ogre_library_dirs}
  ${Boost_LIBRARY_DIRS}
  ${ODE_LIBRARY_DIRS}
  ${SDFormat_LIBRARY_DIRS}
)

if (HAVE_BULLET)
  link_directories(${BULLET_LIBRARY_DIRS})
endif()

configure_file (test_config.h.in ${PROJECT_BINARY_DIR}/test_config.h)

# Build gtest
add_library(gtest STATIC ${GTEST_SRC_DIR}/src/gtest-all.cc)
add_library(gtest_main STATIC ${GTEST_SRC_DIR}/src/gtest_main.cc)
target_link_libraries(gtest_main gtest)
set(GTEST_LIBRARY "${PROJECT_BINARY_DIR}/test/libgtest.a")
set(GTEST_MAIN_LIBRARY "${PROJECT_BINARY_DIR}/test/libgtest_main.a")

execute_process(COMMAND cmake -E remove_directory ${CMAKE_BINARY_DIR}/test_results)
execute_process(COMMAND cmake -E make_directory ${CMAKE_BINARY_DIR}/test_results)
include_directories(${GTEST_INCLUDE_DIRS})

include_directories(${PROJECT_SOURCE_DIR}/test)

add_subdirectory(plugins)

set(TEST_TYPE "PERFORMANCE")
add_subdirectory(performance)
set(TEST_TYPE "INTEGRATION")
add_subdirectory(integration)
set(TEST_TYPE "EXAMPLE")
add_subdirectory(examples)
set(TEST_TYPE "REGRESSION")
add_subdirectory(regression)
add_subdirectory(cmake)
add_subdirectory(pkgconfig)
