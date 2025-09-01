cmake_minimum_required(VERSION 3.15)
project(librouter VERSION 0.1.0)

# Configure header file
configure_file(src/version.h.in include/router/version.h @ONLY)

add_library(librouter)

target_sources(librouter PRIVATE
    src/config.c
    src/history.c
    src/location.c
    src/matcher.c
    src/route_record.c
    src/route.c
    src/router.c
    src/strmap.c
    src/utils.c
)

target_include_directories(librouter
    PUBLIC
        include
        ${CMAKE_BINARY_DIR}/lib/router/include/router
)

target_link_libraries(librouter
    PUBLIC
        yutil
        libui
)

if(BUILD_SHARED_LIBS)
    target_compile_definitions(librouter PRIVATE LIBROUTER_DLL_EXPORT)
    if(WIN32)
        # Windows specific settings for shared library
    endif()
else()
    target_compile_definitions(librouter PRIVATE LIBROUTER_STATIC_BUILD=1)
endif()

# Install header files
install(FILES include/router.h DESTINATION include)
install(DIRECTORY include/router DESTINATION include)

# Install library
install(TARGETS librouter
    EXPORT librouterTargets
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
)

# Test target
option(BUILD_ROUTER_TESTS "Build router tests" OFF)
if(BUILD_ROUTER_TESTS)
    add_executable(librouter_tests)
    
    target_sources(librouter_tests PRIVATE
        tests/test.c
    )
    
    target_link_libraries(librouter_tests
        PRIVATE
            librouter
            ctest
    )
endif()
