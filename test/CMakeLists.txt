
add_executable(test_basic test.cpp)
add_executable(test_graph test_graph.cpp)

# add library
target_link_libraries(test_basic gcpp)
target_link_libraries(test_graph gcpp)

enable_testing()

add_test(test_basic ${CMAKE_BINARY_DIR}/test_basic -s)

# FIXME: Fix graph tests which fail with an assertion error on macOS.
add_test(test_graph ${CMAKE_BINARY_DIR}/test_graph -s)