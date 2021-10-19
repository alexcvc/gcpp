cmake_minimum_required(VERSION 3.1)
project(gcpp CXX)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED on)

# creates a library gcpp which is an interface (header files only)
add_library(gcpp INTERFACE)

# when minimum version required is 3.8.0 remove if below
# both branches do exactly the same thing
if (CMAKE_MAJOR_VERSION VERSION_LESS 3.7.9)
    if (NOT MSVC)
        include(CheckCXXCompilerFlag)
        CHECK_CXX_COMPILER_FLAG("-std=c++14" COMPILER_SUPPORTS_CXX14)
        if (COMPILER_SUPPORTS_CXX14)
            target_compile_options(gcpp INTERFACE "-std=c++14")
        else ()
            message(FATAL_ERROR "The compiler ${CMAKE_CXX_COMPILER} has no C++14 support. Please use a different C++ compiler.")
        endif ()
    endif ()
else ()
    # set the gcpp library to be compiled only with c++14
    target_compile_features(gcpp INTERFACE cxx_std_14)
    # on *nix systems force the use of -std=c++XX instead of -std=gnu++XX (default)
    set(CMAKE_CXX_EXTENSIONS OFF)
endif ()

# add definitions to the library and targets that consume it
target_compile_definitions(gcpp INTERFACE
        $<$<CXX_COMPILER_ID:MSVC>:
        # remove unnecessary warnings about unchecked iterators
        _SCL_SECURE_NO_WARNINGS
        >
        )

# add include folders to the library and targets that consume it
target_include_directories(gcpp INTERFACE
        $<BUILD_INTERFACE:${CMAKE_SOURCE_DIR}/include>
        )

install(
        DIRECTORY include/gcpp
        DESTINATION include
)


include_directories(SYSTEM include)
include_directories(SYSTEM submodules/gsl/include)

add_executable(test_basic test.cpp)
add_executable(test_graph test_graph.cpp)

# add library
target_link_libraries(test_basic gcpp)
target_link_libraries(test_graph gcpp)

enable_testing()

add_test(test_basic ${CMAKE_BINARY_DIR}/test_basic -s)

# FIXME: Fix graph tests which fail with an assertion error on macOS.
add_test(test_graph ${CMAKE_BINARY_DIR}/test_graph -s)
