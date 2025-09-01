cmake_minimum_required(VERSION 3.15)
project(libptk VERSION 0.1.0)

# Find optional packages for Linux
if(UNIX AND NOT APPLE)
    find_package(PkgConfig QUIET)
    if(PKG_CONFIG_FOUND)
        pkg_check_modules(X11 x11)
    endif()
endif()

# Options
option(ENABLE_TOUCH "Enable touch support" ON)

# Configure header file
if(ENABLE_TOUCH)
    set(PTK_TOUCH_ENABLED 1)
endif()
if(X11_FOUND)
    set(PTK_HAS_LIBX11 1)
endif()

configure_file(src/config.h.in include/ptk/config.h @ONLY)

add_library(libptk)

# Base source files
target_sources(libptk PRIVATE
    src/app.c
    src/clipboard.c
    src/events.c
    src/ime.c
    src/steptimer.c
)

# Platform-specific source files
if(WIN32)
    file(GLOB WINDOWS_SOURCES "src/windows/*.c")
    target_sources(libptk PRIVATE ${WINDOWS_SOURCES})
    target_link_libraries(libptk PRIVATE Shell32)
else()
    file(GLOB LINUX_SOURCES "src/linux/*.c")
    target_sources(libptk PRIVATE ${LINUX_SOURCES})
    target_link_libraries(libptk PRIVATE pthread dl)
    
    if(X11_FOUND)
        target_link_libraries(libptk PRIVATE ${X11_LIBRARIES})
        target_include_directories(libptk PRIVATE ${X11_INCLUDE_DIRS})
    endif()
endif()

target_include_directories(libptk
    PUBLIC
        include
        ${CMAKE_BINARY_DIR}/lib/ptk/include/ptk
)

target_link_libraries(libptk
    PUBLIC
        yutil
        pandagl
)

if(BUILD_SHARED_LIBS)
    target_compile_definitions(libptk PRIVATE PTK_DLL_EXPORT)
    if(WIN32)
        # Windows specific settings for shared library
    endif()
else()
    target_compile_definitions(libptk PRIVATE PTK_STATIC_BUILD=1)
endif()

# Install header files
install(FILES include/ptk.h DESTINATION include)
install(DIRECTORY include/ptk DESTINATION include)

# Install library
install(TARGETS libptk
    EXPORT libptkTargets
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
)
