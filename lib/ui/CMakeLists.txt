cmake_minimum_required(VERSION 3.15)
project(libui VERSION 0.1.0)

# Configure header file
configure_file(src/config.h.in include/ui/config.h @ONLY)

add_library(libui)

# Add all C files recursively
file(GLOB_RECURSE UI_SOURCES "src/*.c")
target_sources(libui PRIVATE ${UI_SOURCES})

target_include_directories(libui
    PUBLIC
        include
        ${CMAKE_BINARY_DIR}/lib/ui/include/ui
)

target_link_libraries(libui
    PUBLIC
        yutil
        pandagl
        libcss
)

if(BUILD_SHARED_LIBS)
    target_compile_definitions(libui PRIVATE LIBUI_DLL_EXPORT)
    if(WIN32)
        # Windows specific settings for shared library
    endif()
else()
    target_compile_definitions(libui PRIVATE LIBUI_STATIC_BUILD=1)
endif()

# Install header files
install(FILES include/ui.h DESTINATION include)
install(DIRECTORY include/ui DESTINATION include)

# Install library
install(TARGETS libui
    EXPORT libuiTargets
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
)
