cmake_minimum_required(VERSION 3.15)
project(lcui VERSION 3.0.0)

# Set C standard
set(CMAKE_C_STANDARD 99)
set(CMAKE_C_STANDARD_REQUIRED ON)

# Set policy for package requirements lock (equivalent behavior)
cmake_policy(SET CMP0074 NEW)

# Build type and modes
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

# Add custom build types
set(CMAKE_CONFIGURATION_TYPES "Debug;Release;Coverage" CACHE STRING "Available build configurations" FORCE)

# Set warnings as errors (but allow some flexibility for existing code)
if(MSVC)
    add_compile_options(/W3)
    # Don't treat warnings as errors for existing code
    # add_compile_options(/WX)
else()
    add_compile_options(-Wall -Wextra)
    # Don't treat warnings as errors for existing code  
    # add_compile_options(-Werror)
endif()

# Add RPATH settings for shared libraries
if(APPLE)
    set(CMAKE_INSTALL_RPATH "@loader_path/lib;@loader_path")
elseif(UNIX)
    set(CMAKE_INSTALL_RPATH "$ORIGIN/lib:$ORIGIN")
endif()

# Global definitions
add_compile_definitions(
    UNICODE 
    _UNICODE
    YUTIL_EXPORTS
)

if(WIN32)
    add_compile_definitions(_CRT_SECURE_NO_WARNINGS)
else()
    add_compile_options(-fPIC)
    
    # Coverage mode support
    if(CMAKE_BUILD_TYPE STREQUAL "Coverage")
        add_compile_options(-ftest-coverage -fprofile-arcs)
        link_libraries(gcov)
    endif()
endif()

# Options
option(BUILD_SHARED_LIBS "Build shared libraries" OFF)
option(BUILD_TESTS "Build test targets" OFF)
option(CI_ENV "Enable CI environment settings" OFF)

if(CI_ENV)
    add_compile_definitions(CI_ENV)
endif()

# Include directories (public)
include_directories(
    lib/ctest/include
    lib/yutil/include
    lib/pandagl/include
    lib/thread/include
    lib/css/include
    lib/i18n/include
    lib/ptk/include
    lib/worker/include
    lib/router/include
    lib/ui/include
    lib/ui-server/include
    lib/ui-cursor/include
    lib/ui-xml/include
    include
    ${CMAKE_CURRENT_BINARY_DIR}/include
    ${CMAKE_CURRENT_BINARY_DIR}/lib/css/include
    ${CMAKE_CURRENT_BINARY_DIR}/lib/pandagl/include
    ${CMAKE_CURRENT_BINARY_DIR}/lib/worker/include
    ${CMAKE_CURRENT_BINARY_DIR}/lib/ptk/include
    ${CMAKE_CURRENT_BINARY_DIR}/lib/ui/include
    ${CMAKE_CURRENT_BINARY_DIR}/lib/i18n/include
    ${CMAKE_CURRENT_BINARY_DIR}/lib/router/include
    ${CMAKE_CURRENT_BINARY_DIR}/lib/ui-cursor/include
    ${CMAKE_CURRENT_BINARY_DIR}/lib/ui-xml/include
    ${CMAKE_CURRENT_BINARY_DIR}/lib/ui-server/include
    ${CMAKE_CURRENT_BINARY_DIR}/lib/yutil/include
    ${CMAKE_CURRENT_BINARY_DIR}/lib/thread/include
)

# Add subdirectories
add_subdirectory(lib)

# Set version information for main config
set(VERSION "3.0.0")
execute_process(
    COMMAND git rev-parse --short HEAD
    WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}
    OUTPUT_VARIABLE GIT_COMMIT
    OUTPUT_STRIP_TRAILING_WHITESPACE
    ERROR_QUIET
)
if(NOT GIT_COMMIT)
    set(GIT_COMMIT "unknown")
endif()

# Configure main LCUI library header
configure_file(src/config.h.in include/LCUI/config.h @ONLY)

# Main LCUI library
add_library(lcui)

# Add all source files recursively
file(GLOB_RECURSE LCUI_SOURCES "src/*.c")
target_sources(lcui PRIVATE ${LCUI_SOURCES})

target_include_directories(lcui
    PUBLIC
        include
        ${CMAKE_CURRENT_BINARY_DIR}/include
)

# Library dependencies (in correct order)
target_link_libraries(lcui
    PUBLIC
        yutil
        libthread
        libptk
        libworker
        pandagl
        libcss
        libi18n
        libui
        libui-cursor
        libui-server
        librouter
        libui-xml
)

# Check for fontconfig
find_package(PkgConfig QUIET)
if(PKG_CONFIG_FOUND)
    pkg_check_modules(FONTCONFIG fontconfig)
    if(FONTCONFIG_FOUND)
        add_compile_definitions(HAVE_FONTCONFIG)
        target_link_libraries(lcui PRIVATE ${FONTCONFIG_LIBRARIES})
        target_include_directories(lcui PRIVATE ${FONTCONFIG_INCLUDE_DIRS})
    endif()
endif()

# Configure static/shared build
if(BUILD_SHARED_LIBS)
    if(WIN32)
        target_compile_definitions(lcui PRIVATE LCUI_DLL_EXPORT)
    endif()
else()
    target_compile_definitions(lcui PRIVATE LCUI_STATIC_BUILD=1)
endif()

# Install headers
install(FILES include/LCUI.h DESTINATION include)
install(DIRECTORY include/LCUI DESTINATION include)

# Install library
install(TARGETS lcui
    EXPORT lcuiTargets
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
)

# Add tests subdirectory
if(BUILD_TESTS)
    add_subdirectory(tests)
endif()
