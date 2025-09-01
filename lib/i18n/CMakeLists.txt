cmake_minimum_required(VERSION 3.15)
project(i18n VERSION 0.1.0)

# Find optional packages
find_package(PkgConfig QUIET)
if(PKG_CONFIG_FOUND)
    pkg_check_modules(YAML yaml-0.1)
endif()

# Configure header file
if(YAML_FOUND)
    set(LIBI18N_HAS_YAML 1)
endif()

configure_file(src/i18n.h.in include/i18n.h @ONLY)

add_library(libi18n)

set(SOURCES
    src/i18n-dict.c
    src/i18n.c
)
if (YAML_FOUND)
    set(SOURCES ${SOURCES} src/yaml.c)
endif()

target_sources(libi18n PRIVATE ${SOURCES})

target_include_directories(libi18n
    PUBLIC
        include
        ${CMAKE_BINARY_DIR}/lib/i18n/include/i18n
)

target_link_libraries(libi18n
    PUBLIC
        yutil
)

# Link optional libraries
if(YAML_FOUND)
    target_link_libraries(libi18n PRIVATE ${YAML_LIBRARIES})
    target_include_directories(libi18n PRIVATE ${YAML_INCLUDE_DIRS})
endif()

if(BUILD_SHARED_LIBS)
    target_compile_definitions(libi18n PRIVATE LIBI18N_DLL_EXPORT)
    if(WIN32)
        # Windows specific settings for shared library
    endif()
else()
    target_compile_definitions(libi18n PRIVATE LIBI18N_STATIC_BUILD=1)
endif()

# Install header files
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/include/i18n.h DESTINATION include)

# Install library
install(TARGETS libi18n
    EXPORT libi18nTargets
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
)

# Test target
option(BUILD_I18N_TESTS "Build i18n tests" OFF)
if(BUILD_I18N_TESTS)
    add_executable(libi18n_tests)
    
    target_sources(libi18n_tests PRIVATE
        tests/test.c
    )
    
    target_link_libraries(libi18n_tests
        PRIVATE
            libi18n
            ctest
    )
    
    set_target_properties(libi18n_tests PROPERTIES
        RUNTIME_OUTPUT_DIRECTORY tests
    )
endif()
