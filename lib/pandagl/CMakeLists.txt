cmake_minimum_required(VERSION 3.15)
project(pandagl VERSION 0.1.0)

# Find optional packages
find_package(PkgConfig QUIET)
find_package(PNG QUIET)
find_package(JPEG QUIET)
find_package(Freetype QUIET)

if(PKG_CONFIG_FOUND AND UNIX AND NOT APPLE)
    pkg_check_modules(FONTCONFIG fontconfig)
endif()

# Options for features
option(WITH_PANDAGL_FONT "Enable font support" ON)
option(WITH_PANDAGL_IMAGE "Enable image support" ON)
option(WITH_PANDAGL_TEXT "Enable text support" ON)

# Configure header file
if(WITH_PANDAGL_FONT)
    set(PANDAGL_HAS_FONT 1)
endif()
if(WITH_PANDAGL_IMAGE)
    set(PANDAGL_HAS_IMAGE 1)
endif()
if(WITH_PANDAGL_TEXT)
    set(PANDAGL_HAS_TEXT 1)
endif()
if(Freetype_FOUND)
    set(PANDAGL_HAS_FREETYPE 1)
endif()
if(FONTCONFIG_FOUND)
    set(PANDAGL_HAS_FONTCONFIG 1)
endif()
if(PNG_FOUND)
    set(PANDAGL_HAS_LIBPNG 1)
endif()
if(JPEG_FOUND)
    set(PANDAGL_HAS_LIBJPEG 1)
endif()

configure_file(src/config.h.in include/pandagl/config.h @ONLY)

add_library(pandagl)

# Base source files
target_sources(pandagl PRIVATE
    src/background.c
    src/border.c
    src/boxshadow.c
    src/canvas.c
    src/context.c
    src/file_reader.c
    src/flip.c
    src/line.c
    src/pixel.c
    src/rect.c
    src/tile.c
    src/zoom.c
)

# Conditional source files
if(WITH_PANDAGL_FONT)
    file(GLOB FONT_SOURCES "src/font/*.c")
    target_sources(pandagl PRIVATE ${FONT_SOURCES})
endif()

if(WITH_PANDAGL_TEXT)
    file(GLOB TEXT_SOURCES "src/text/*.c")
    target_sources(pandagl PRIVATE ${TEXT_SOURCES})
endif()

if(WITH_PANDAGL_IMAGE)
    file(GLOB IMAGE_SOURCES "src/image/*.c")
    target_sources(pandagl PRIVATE ${IMAGE_SOURCES})
endif()

target_include_directories(pandagl
    PUBLIC
        include
        ${CMAKE_BINARY_DIR}/lib/pandagl/include/pandagl
)

target_link_libraries(pandagl
    PUBLIC
        yutil
)

# Link optional libraries
if(PNG_FOUND)
    target_link_libraries(pandagl PRIVATE PNG::PNG)
endif()

if(JPEG_FOUND)
    target_link_libraries(pandagl PRIVATE ${JPEG_LIBRARIES})
    target_include_directories(pandagl PRIVATE ${JPEG_INCLUDE_DIRS})
endif()

if(Freetype_FOUND)
    target_link_libraries(pandagl PRIVATE ${FREETYPE_LIBRARIES})
    target_include_directories(pandagl PRIVATE ${FREETYPE_INCLUDE_DIRS})
endif()

if(FONTCONFIG_FOUND)
    target_link_libraries(pandagl PRIVATE ${FONTCONFIG_LIBRARIES})
    target_include_directories(pandagl PRIVATE ${FONTCONFIG_INCLUDE_DIRS})
endif()

if(BUILD_SHARED_LIBS)
    target_compile_definitions(pandagl PRIVATE PANDAGL_DLL_EXPORT)
    if(WIN32)
        # Windows specific settings for shared library
    endif()
else()
    target_compile_definitions(pandagl PRIVATE PANDAGL_STATIC_BUILD=1)
endif()

# Install header files
install(FILES include/pandagl.h DESTINATION include)
install(DIRECTORY include/pandagl DESTINATION include)

# Install library
install(TARGETS pandagl
    EXPORT pandaglTargets
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
)

# Test target
option(BUILD_PANDAGL_TESTS "Build pandagl tests" OFF)
if(BUILD_PANDAGL_TESTS)
    add_executable(pandagl_tests)
    
    file(GLOB TEST_SOURCES "test/*.c")
    target_sources(pandagl_tests PRIVATE ${TEST_SOURCES})
    
    target_link_libraries(pandagl_tests
        PRIVATE
            ctest
            pandagl
    )
endif()
