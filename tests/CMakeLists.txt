cmake_minimum_required(VERSION 3.15)

# Include directories for tests
include_directories(include)

# Common settings for all test targets
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR})

# Main test runner
add_executable(lcui_tests)

target_sources(lcui_tests PRIVATE
    run_tests.c
)

# Add all test cases
file(GLOB TEST_CASE_SOURCES "cases/*.c")
target_sources(lcui_tests PRIVATE ${TEST_CASE_SOURCES})

target_link_libraries(lcui_tests
    PRIVATE
        ctest
        lcui
)

# Make tests not built by default
set_target_properties(lcui_tests PROPERTIES
    EXCLUDE_FROM_ALL TRUE
)

# Individual test targets
add_executable(test_block_layout test_block_layout.c)
target_link_libraries(test_block_layout PRIVATE ctest lcui)
set_target_properties(test_block_layout PROPERTIES EXCLUDE_FROM_ALL TRUE)

add_executable(test_border test_border.c)
target_link_libraries(test_border PRIVATE lcui)
set_target_properties(test_border PROPERTIES EXCLUDE_FROM_ALL TRUE)

add_executable(test_box_shadow test_box_shadow.c)
target_link_libraries(test_box_shadow PRIVATE lcui)
set_target_properties(test_box_shadow PROPERTIES EXCLUDE_FROM_ALL TRUE)

add_executable(test_char_render test_char_render.c)
target_link_libraries(test_char_render PRIVATE lcui)
set_target_properties(test_char_render PROPERTIES EXCLUDE_FROM_ALL TRUE)

add_executable(test_fill_rect test_fill_rect.c)
target_link_libraries(test_fill_rect PRIVATE lcui)
set_target_properties(test_fill_rect PROPERTIES EXCLUDE_FROM_ALL TRUE)

add_executable(test_fill_rect_with_rgba test_fill_rect_with_rgba.c)
target_link_libraries(test_fill_rect_with_rgba PRIVATE lcui)
set_target_properties(test_fill_rect_with_rgba PROPERTIES EXCLUDE_FROM_ALL TRUE)

add_executable(test_flex_layout test_flex_layout.c)
target_link_libraries(test_flex_layout PRIVATE ctest lcui)
set_target_properties(test_flex_layout PROPERTIES EXCLUDE_FROM_ALL TRUE)

add_executable(test_image_scaling_bench test_image_scaling_bench.c)
target_link_libraries(test_image_scaling_bench PRIVATE lcui)
set_target_properties(test_image_scaling_bench PROPERTIES EXCLUDE_FROM_ALL TRUE)

add_executable(test_mix_rect_with_opacity test_mix_rect_with_opacity.c)
target_link_libraries(test_mix_rect_with_opacity PRIVATE lcui)
set_target_properties(test_mix_rect_with_opacity PROPERTIES EXCLUDE_FROM_ALL TRUE)

add_executable(test_paint_background test_paint_background.c)
target_link_libraries(test_paint_background PRIVATE lcui)
set_target_properties(test_paint_background PROPERTIES EXCLUDE_FROM_ALL TRUE)

add_executable(test_paint_border test_paint_border.c)
target_link_libraries(test_paint_border PRIVATE lcui)
set_target_properties(test_paint_border PROPERTIES EXCLUDE_FROM_ALL TRUE)

add_executable(test_paint_boxshadow test_paint_boxshadow.c)
target_link_libraries(test_paint_boxshadow PRIVATE lcui)
set_target_properties(test_paint_boxshadow PROPERTIES EXCLUDE_FROM_ALL TRUE)

add_executable(test_pixel_manipulation test_pixel_manipulation.c)
target_link_libraries(test_pixel_manipulation PRIVATE lcui)
set_target_properties(test_pixel_manipulation PROPERTIES EXCLUDE_FROM_ALL TRUE)

add_executable(test_render test_render.c)
target_link_libraries(test_render PRIVATE lcui)
set_target_properties(test_render PROPERTIES EXCLUDE_FROM_ALL TRUE)

add_executable(test_scaling_support test_scaling_support.c)
target_link_libraries(test_scaling_support PRIVATE lcui)
set_target_properties(test_scaling_support PROPERTIES EXCLUDE_FROM_ALL TRUE)

add_executable(test_scrollbar test_scrollbar.c)
target_link_libraries(test_scrollbar PRIVATE ctest lcui)
set_target_properties(test_scrollbar PROPERTIES EXCLUDE_FROM_ALL TRUE)

add_executable(test_string_render test_string_render.c)
target_link_libraries(test_string_render PRIVATE lcui)
set_target_properties(test_string_render PROPERTIES EXCLUDE_FROM_ALL TRUE)

add_executable(test_text_resize test_text_resize.c)
target_link_libraries(test_text_resize PRIVATE ctest lcui)
set_target_properties(test_text_resize PROPERTIES EXCLUDE_FROM_ALL TRUE)

add_executable(test_touch test_touch.c)
target_link_libraries(test_touch PRIVATE lcui)
set_target_properties(test_touch PROPERTIES EXCLUDE_FROM_ALL TRUE)

add_executable(test_widget test_widget.c)
target_link_libraries(test_widget PRIVATE lcui)
set_target_properties(test_widget PROPERTIES EXCLUDE_FROM_ALL TRUE)

add_executable(test_widget_opacity test_widget_opacity.c)
target_link_libraries(test_widget_opacity PRIVATE ctest lcui)
set_target_properties(test_widget_opacity PROPERTIES EXCLUDE_FROM_ALL TRUE)

add_executable(test_widget_render test_widget_render.c)
target_link_libraries(test_widget_render PRIVATE lcui)
set_target_properties(test_widget_render PROPERTIES EXCLUDE_FROM_ALL TRUE)

# Custom target to build all tests
add_custom_target(all_tests
    DEPENDS
        lcui_tests
        test_block_layout
        test_border
        test_box_shadow
        test_char_render
        test_fill_rect
        test_fill_rect_with_rgba
        test_flex_layout
        test_image_scaling_bench
        test_mix_rect_with_opacity
        test_paint_background
        test_paint_border
        test_paint_boxshadow
        test_pixel_manipulation
        test_render
        test_scaling_support
        test_scrollbar
        test_string_render
        test_text_resize
        test_touch
        test_widget
        test_widget_opacity
        test_widget_render
)
