cmake_minimum_required(VERSION 3.15)
project(libthread VERSION 0.1.0)

# Configure header file
configure_file(src/thread.h.in include/thread.h @ONLY)

add_library(libthread)

target_sources(libthread PRIVATE
    src/pthread.c
    src/windows.c
)

target_include_directories(libthread
    PUBLIC
        include
        ${CMAKE_BINARY_DIR}/lib/thread/include/thread
)

target_link_libraries(libthread
    PUBLIC
        yutil
)

if(BUILD_SHARED_LIBS)
    target_compile_definitions(libthread PRIVATE LIBTHREAD_DLL_EXPORT)
    if(WIN32)
        # Windows specific settings for shared library
    endif()
else()
    target_compile_definitions(libthread PRIVATE LIBTHREAD_STATIC_BUILD=1)
endif()

# Platform-specific system libraries
if(NOT WIN32)
    target_link_libraries(libthread PUBLIC pthread dl)
endif()

# Install header files
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/include/thread.h DESTINATION include)

# Install library
install(TARGETS libthread
    EXPORT libthreadTargets
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
)
