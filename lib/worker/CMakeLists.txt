cmake_minimum_required(VERSION 3.15)
project(libworker VERSION 0.1.0)

# Configure header file
configure_file(src/config.h.in include/worker/config.h @ONLY)

add_library(libworker)

# Add all C files recursively
file(GLOB_RECURSE WORKER_SOURCES "src/*.c")
target_sources(libworker PRIVATE ${WORKER_SOURCES})

target_include_directories(libworker
    PUBLIC
        include
        ${CMAKE_BINARY_DIR}/lib/worker/include/worker
)

target_link_libraries(libworker
    PUBLIC
        yutil
        libthread
)

if(BUILD_SHARED_LIBS)
    target_compile_definitions(libworker PRIVATE LIBWORKER_DLL_EXPORT)
    if(WIN32)
        # Windows specific settings for shared library
    endif()
else()
    target_compile_definitions(libworker PRIVATE LIBWORKER_STATIC_BUILD=1)
endif()

# Install header files
install(FILES include/worker.h DESTINATION include)
install(DIRECTORY include/worker DESTINATION include)

# Install library
install(TARGETS libworker
    EXPORT libworkerTargets
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
)
