# Crated by mbenlioglu on Oct 20, 2020.
# -------------------------------------------------------------

file(GLOB test_files "test_*.cpp")

foreach(test_src ${test_files})
    get_filename_component(exe ${test_src} NAME_WE)
    add_executable(${exe} ${test_src})
    target_include_directories(${exe} PUBLIC
            "${CMAKE_SOURCE_DIR}/include"
            "${CMAKE_SOURCE_DIR}/src")
    target_link_libraries(${exe} PUBLIC mmio)

    add_test(NAME ${exe} COMMAND
            ${CMAKE_CURRENT_BINARY_DIR}/${exe}
            WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR})
    set_tests_properties(${exe}
            PROPERTIES
            TIMEOUT 100)
endforeach()
