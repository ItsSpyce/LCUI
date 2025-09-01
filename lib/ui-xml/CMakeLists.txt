cmake_minimum_required(VERSION 3.15)
project(libui-xml VERSION 0.1.0)

# Find optional packages
find_package(PkgConfig QUIET)
if(PKG_CONFIG_FOUND)
    pkg_check_modules(LIBXML2 libxml-2.0)
endif()

# Configure header file
if(LIBXML2_FOUND)
    set(LIBUI_XML_HAS_LIBXML2 1)
endif()

configure_file(src/config.h.in include/ui_xml/config.h @ONLY)

add_library(libui-xml)

# Add all C files recursively
file(GLOB_RECURSE UI_XML_SOURCES "src/*.c")
target_sources(libui-xml PRIVATE ${UI_XML_SOURCES})

target_include_directories(libui-xml
    PUBLIC
        include
        ${CMAKE_BINARY_DIR}/lib/ui-xml/include/ui-xml
)

target_link_libraries(libui-xml
    PUBLIC
        yutil
        libui
        libcss
        pandagl
)

# Link optional libraries
if(LIBXML2_FOUND)
    target_link_libraries(libui-xml PRIVATE ${LIBXML2_LIBRARIES})
    target_include_directories(libui-xml PRIVATE ${LIBXML2_INCLUDE_DIRS})
endif()

if(BUILD_SHARED_LIBS)
    target_compile_definitions(libui-xml PRIVATE LIBUI_XML_DLL_EXPORT)
    if(WIN32)
        # Windows specific settings for shared library
    endif()
else()
    target_compile_definitions(libui-xml PRIVATE LIBUI_XML_STATIC_BUILD=1)
endif()

# Install header files
install(FILES include/ui_xml.h DESTINATION include)
install(DIRECTORY include/ui_xml DESTINATION include)

# Install library
install(TARGETS libui-xml
    EXPORT libui-xmlTargets
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
)
