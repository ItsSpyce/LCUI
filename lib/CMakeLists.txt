cmake_minimum_required(VERSION 3.15)
project(LCUI_Libraries VERSION 0.1.0)

# Set C standard
set(CMAKE_C_STANDARD 99)
set(CMAKE_C_STANDARD_REQUIRED ON)

# Build options
option(BUILD_SHARED_LIBS "Build shared libraries" OFF)
option(BUILD_TESTS "Build test targets" OFF)

# Add subdirectories in dependency order
add_subdirectory(yutil)
add_subdirectory(ctest)
add_subdirectory(thread)
add_subdirectory(worker)
add_subdirectory(pandagl)
add_subdirectory(css)
add_subdirectory(ptk)
add_subdirectory(ui)
add_subdirectory(i18n)
add_subdirectory(router)
add_subdirectory(ui-cursor)
add_subdirectory(ui-xml)
add_subdirectory(ui-server)

# Optionally add pandagl examples
option(BUILD_PANDAGL_EXAMPLES "Build pandagl examples" OFF)
if(BUILD_PANDAGL_EXAMPLES)
    add_subdirectory(pandagl/examples/cairo)
endif()

# Set global compile definitions
if(WIN32)
    add_compile_definitions(_UNICODE UNICODE)
    if(MSVC)
        add_compile_definitions(_CRT_SECURE_NO_WARNINGS)
    endif()
else()
    add_compile_definitions(_GNU_SOURCE=1)
endif()

# Global compiler flags
if(CMAKE_C_COMPILER_ID STREQUAL "GNU" OR CMAKE_C_COMPILER_ID STREQUAL "Clang")
    add_compile_options(-Wall -Wextra)
    add_compile_options(-Wno-deprecated-declarations -fno-strict-aliasing)
endif()

# Set optimization level
if(CMAKE_BUILD_TYPE STREQUAL "Release")
    if(MSVC)
        add_compile_options(/O2)
    else()
        add_compile_options(-O3)
    endif()
endif()
