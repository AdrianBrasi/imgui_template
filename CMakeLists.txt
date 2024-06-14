cmake_minimum_required(VERSION 3.10)

# Project name
project(myproject)

# Set C++ standard
set(CMAKE_CXX_STANDARD 11)

# Set output directories
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/../bin)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/../bin)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/../bin)

# Source files
set(IMGUI_DIR src/../imgui)
set(BACKENDS_DIR src/../backends)
set(SOURCES
        src/main.cpp
        ${IMGUI_DIR}/imgui.cpp
        ${IMGUI_DIR}/imgui_demo.cpp
        ${IMGUI_DIR}/imgui_draw.cpp
        ${IMGUI_DIR}/imgui_tables.cpp
        ${IMGUI_DIR}/imgui_widgets.cpp
        ${BACKENDS_DIR}/imgui_impl_glfw.cpp
        ${BACKENDS_DIR}/imgui_impl_opengl3.cpp
)

# Include directories
include_directories(${IMGUI_DIR} ${BACKENDS_DIR})

# Platform-specific settings
if(UNIX AND NOT APPLE)
    message(STATUS "Configuring for Linux")
    find_package(PkgConfig REQUIRED)
    pkg_check_modules(GLFW REQUIRED glfw3)
    include_directories(${GLFW_INCLUDE_DIRS})
    link_directories(${GLFW_LIBRARY_DIRS})
    add_definitions(${GLFW_CFLAGS_OTHER})
    set(PLATFORM_LIBS ${GLFW_LIBRARIES} GL)
elseif(WIN32)
    message(STATUS "Configuring for Windows")
    set(PLATFORM_LIBS glfw3 gdi32 opengl32 imm32)
endif()

# Add executable
add_executable(${PROJECT_NAME} ${SOURCES})

# Link libraries
target_link_libraries(${PROJECT_NAME} ${PLATFORM_LIBS})

# Set compiler flags
if(UNIX AND NOT APPLE)
    target_compile_options(${PROJECT_NAME} PRIVATE -g -Wall -Wformat)
endif()

# Custom clean target
add_custom_target(clean-all
        COMMAND ${CMAKE_COMMAND} -P clean-all.cmake
)

# Write a custom clean script
file(WRITE ${CMAKE_BINARY_DIR}/clean-all.cmake
        "file(REMOVE_RECURSE ${CMAKE_RUNTIME_OUTPUT_DIRECTORY})
file(REMOVE_RECURSE ${CMAKE_ARCHIVE_OUTPUT_DIRECTORY})
file(REMOVE_RECURSE ${CMAKE_LIBRARY_OUTPUT_DIRECTORY})"
)
