cmake_minimum_required(VERSION 3.15)
project(libcss VERSION 0.1.0)

# Configure header file
configure_file(src/config.h.in include/css/config.h @ONLY)

add_library(libcss)

# Add all C files recursively
file(GLOB_RECURSE CSS_SOURCES "src/*.c")
target_sources(libcss PRIVATE ${CSS_SOURCES})

target_include_directories(libcss
    PUBLIC
        include
        ${CMAKE_BINARY_DIR}/lib/css/include/css
)

target_link_libraries(libcss
    PUBLIC
        yutil
)

if(BUILD_SHARED_LIBS)
    target_compile_definitions(libcss PRIVATE LIBCSS_DLL_EXPORT)
    if(WIN32)
        # Windows specific settings for shared library
    endif()
else()
    target_compile_definitions(libcss PRIVATE LIBCSS_STATIC_BUILD=1)
endif()

# Install header files
install(FILES include/css.h DESTINATION include)
install(DIRECTORY include/css DESTINATION include)

# Install library
install(TARGETS libcss
    EXPORT libcssTargets
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
)

# Test target
option(BUILD_CSS_TESTS "Build CSS tests" OFF)
if(BUILD_CSS_TESTS)
    add_executable(libcss_tests)
    
    target_sources(libcss_tests PRIVATE
        tests/test_css_computed.c
        tests/test_css_keywords.c
        tests/test_css_value.c
        tests/test.c
    )
    
    target_link_libraries(libcss_tests
        PRIVATE
            ctest
            libcss
    )
    
    set_target_properties(libcss_tests PROPERTIES
        RUNTIME_OUTPUT_DIRECTORY tests
    )
endif()
