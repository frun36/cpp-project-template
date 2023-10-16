# Set the project and target names
set(PROJECT_NAME "Project")
set(TARGET_NAME "project")

# Set CMake version and C++ standard
cmake_minimum_required(VERSION 3.22)
set(CMAKE_CXX_STANDARD 11)

# Set the project name and version
project(${PROJECT_NAME} VERSION 1.0 LANGUAGES CXX)

# Specify path to include directory
include_directories(include)

# Get all source .cpp files and add them to the executable target
file(GLOB_RECURSE SRC_FILES "src/*.cpp")
add_executable(${TARGET_NAME} ${SRC_FILES})

# Set compiler flags based on build type
target_compile_options(${TARGET_NAME}
    PRIVATE
    $<$<CONFIG:Debug>:-O0;-g;-Wall;-Wextra>
    $<$<CONFIG:Release>:-O2;-Wall;-Wextra>
)

# Set default build type to `Debug`
set(CMAKE_BUILD_TYPE Debug)

# Enable CTest
enable_testing()

# Add memory test with Valgrind
add_test(
    NAME mem
    COMMAND valgrind --leak-check=full --track-origins=yes --error-exitcode=1 ./${TARGET_NAME}
)

# Add unit tests
file(GLOB_RECURSE TEST_FILES "tests/*.cpp")
foreach(TEST_FILE ${TEST_FILES})
    get_filename_component(TEST_NAME ${TEST_FILE} NAME_WE)
    add_executable(${TEST_NAME} ${TEST_FILE})
    add_test(NAME ${TEST_NAME} COMMAND ./${TEST_NAME})
endforeach()