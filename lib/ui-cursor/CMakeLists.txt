cmake_minimum_required(VERSION 3.15)
project(libui-cursor VERSION 0.1.0)

# Configure header file
configure_file(src/config.h.in include/ui_cursor/config.h @ONLY)

add_library(libui-cursor)

# Add all C files recursively
file(GLOB_RECURSE UI_CURSOR_SOURCES "src/*.c")
target_sources(libui-cursor PRIVATE ${UI_CURSOR_SOURCES})

target_include_directories(libui-cursor
    PUBLIC
        include
        ${CMAKE_BINARY_DIR}/lib/ui-cursor/include/ui-cursor
)

target_link_libraries(libui-cursor
    PUBLIC
        yutil
        pandagl
        libptk
)

if(BUILD_SHARED_LIBS)
    target_compile_definitions(libui-cursor PRIVATE LIBUI_CURSOR_DLL_EXPORT)
    if(WIN32)
        # Windows specific settings for shared library
    endif()
else()
    target_compile_definitions(libui-cursor PRIVATE LIBUI_CURSOR_STATIC_BUILD=1)
endif()

# Install header files
install(FILES include/ui_cursor.h DESTINATION include)
install(DIRECTORY include/ui_cursor DESTINATION include)

# Install library
install(TARGETS libui-cursor
    EXPORT libui-cursorTargets
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
)
