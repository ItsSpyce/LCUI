cmake_minimum_required(VERSION 3.15)
project(libui-server VERSION 0.1.0)

# Find optional packages
find_package(OpenMP QUIET)

# Options
option(WITH_OPENMP "Enable OpenMP support" ON)

# Configure header file
if(OpenMP_FOUND AND WITH_OPENMP)
    set(LIBUI_SERVER_HAS_OPENMP 1)
endif()

configure_file(src/config.h.in include/ui_server/config.h @ONLY)

add_library(libui-server)

# Add all C files recursively
file(GLOB_RECURSE UI_SERVER_SOURCES "src/*.c")
target_sources(libui-server PRIVATE ${UI_SERVER_SOURCES})

target_include_directories(libui-server
    PUBLIC
        include
        ${CMAKE_BINARY_DIR}/lib/ui-server/include/ui-server
)

target_link_libraries(libui-server
    PUBLIC
        yutil
        pandagl
        libptk
        libui
        libui-cursor
)

# Link optional libraries
if(OpenMP_FOUND AND WITH_OPENMP)
    target_link_libraries(libui-server PRIVATE OpenMP::OpenMP_C)
endif()

if(BUILD_SHARED_LIBS)
    target_compile_definitions(libui-server PRIVATE LIBUI_SERVER_DLL_EXPORT)
    if(WIN32)
        # Windows specific settings for shared library
    endif()
else()
    target_compile_definitions(libui-server PRIVATE LIBUI_SERVER_STATIC_BUILD=1)
endif()

# Install header files
install(FILES include/ui_server.h DESTINATION include)
install(DIRECTORY include/ui_server DESTINATION include)

# Install library
install(TARGETS libui-server
    EXPORT libui-serverTargets
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
)
