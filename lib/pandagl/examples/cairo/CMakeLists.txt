cmake_minimum_required(VERSION 3.15)

# Find Cairo package
find_package(PkgConfig REQUIRED)
pkg_check_modules(CAIRO REQUIRED cairo)

# Cairo example executable
add_executable(pandagl_cairo_example)

file(GLOB CAIRO_EXAMPLE_SOURCES "src/*.c")
target_sources(pandagl_cairo_example PRIVATE ${CAIRO_EXAMPLE_SOURCES})

target_link_libraries(pandagl_cairo_example
    PRIVATE
        pandagl
        yutil
        ${CAIRO_LIBRARIES}
)

target_include_directories(pandagl_cairo_example PRIVATE ${CAIRO_INCLUDE_DIRS})

# Build modes
if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    target_compile_definitions(pandagl_cairo_example PRIVATE DEBUG)
elseif(CMAKE_BUILD_TYPE STREQUAL "Release")
    target_compile_definitions(pandagl_cairo_example PRIVATE NDEBUG)
endif()

# Make sure example is not built by default
set_target_properties(pandagl_cairo_example PROPERTIES
    EXCLUDE_FROM_ALL TRUE
)
