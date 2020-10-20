# Crated by mbenlioglu on Oct 20, 2020.
# -------------------------------------------------------------
cmake_minimum_required(VERSION 3.13)
project(mmio
        LANGUAGES CXX
        VERSION 0.0.1
        DESCRIPTION "Library for Graph I/O with Matrix Market format")

# -------------------------------------------------------------
# Default build type

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "Release" CACHE STRING
            "Choose the type of build, options are: Debug Release RelWithDebInfo MinSizeRel."
            FORCE)
endif(NOT CMAKE_BUILD_TYPE)

# -------------------------------------------------------------
# C/CXX Standard and Flags

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Flags
if (MSVC)
    set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /W4 /WX /DDEBUG")
else()
    set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -Wall -Wextra -pedantic -DDEBUG")
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -march=native")
endif ()

set(CMAKE_POSITION_INDEPENDENT_CODE ON)

# -------------------------------------------------------------
# Other flags

set(CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake" ${CMAKE_MODULE_PATH})

# -------------------------------------------------------------
# Compile the main executable
add_library(mmio src/mmio.cpp)
target_include_directories(mmio PUBLIC
        "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
        "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>"
        "$<INSTALL_INTERFACE:include>")

# -------------------------------------------------------------
# Testing
enable_testing()
add_subdirectory(test)

# -------------------------------------------------------------
# Docs
add_subdirectory(docs)

# -------------------------------------------------------------
# Install
include(CMakePackageConfigHelpers)
write_basic_package_version_file(
        "${PROJECT_BINARY_DIR}/mmioConfigVersion.cmake"
        VERSION ${PROJECT_VERSION}
        COMPATIBILITY AnyNewerVersion
)

install(TARGETS mmio
        EXPORT mmioTargets
        LIBRARY DESTINATION lib COMPONENT Runtime
        ARCHIVE DESTINATION lib COMPONENT Development
        RUNTIME DESTINATION bin COMPONENT Runtime
        PUBLIC_HEADER DESTINATION include COMPONENT Development
        BUNDLE DESTINATION bin COMPONENT Runtime
        )
configure_package_config_file(
        "${PROJECT_SOURCE_DIR}/cmake/mmioConfig.cmake.in"
        "${PROJECT_BINARY_DIR}/mmioConfig.cmake"
        INSTALL_DESTINATION lib/cmake/mmio
)

# Define namespace for link_libraries(MMIO::mmio) to work
install(EXPORT mmioTargets NAMESPACE MMIO:: DESTINATION lib/cmake/mmio)
install(FILES "${PROJECT_BINARY_DIR}/mmioConfigVersion.cmake" "${PROJECT_BINARY_DIR}/mmioConfig.cmake"
        DESTINATION lib/cmake/mmio)

# Uninstall
if (NOT TARGET uninstall)
    configure_file(
            "${CMAKE_CURRENT_SOURCE_DIR}/cmake/cmake_uninstall.cmake.in"
            "${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake"
            IMMEDIATE @ONLY
    )
    add_custom_target(uninstall
            COMMAND ${CMAKE_COMMAND} -p ${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake)
endif ()
