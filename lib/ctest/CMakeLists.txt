cmake_minimum_required(VERSION 3.15)

add_library(ctest STATIC)

target_sources(ctest PRIVATE
    src/ctest.c
)

target_include_directories(ctest 
    PUBLIC 
        include
)

target_link_libraries(ctest 
    PUBLIC 
        yutil
)

target_compile_features(ctest PRIVATE c_std_99)

if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    target_compile_definitions(ctest PRIVATE DEBUG)
elseif(CMAKE_BUILD_TYPE STREQUAL "Release")
    target_compile_definitions(ctest PRIVATE NDEBUG)
endif()

# Install header files
install(FILES include/ctest.h DESTINATION include)
