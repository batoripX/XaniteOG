# Android CMakeLists.txt for xemu port
cmake_minimum_required(VERSION 3.22.1)

project("xemu-android")

# Set C/C++ standards
set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)

# Android-specific flags
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DANDROID -D__ANDROID__ -DANDROID_API_LEVEL=${ANDROID_PLATFORM_LEVEL}")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DANDROID -D__ANDROID__ -DANDROID_API_LEVEL=${ANDROID_PLATFORM_LEVEL}")

# Include directories
include_directories(
        .
        include
        linux-headers
)

# Collect source files (simplified - would need full list from meson.build)
file(GLOB_RECURSE XEMU_SOURCES
    XaniteOG/*.c
    XaniteOG/*.cpp
    # Exclude platform-specific files that will be replaced
    # Add Android-specific replacements
)

# Remove desktop-specific files
list(FILTER XEMU_SOURCES EXCLUDE REGEX ".*sdl.*\\.c$")
list(FILTER XEMU_SOURCES EXCLUDE REGEX ".*gtk.*\\.c$")
list(FILTER XEMU_SOURCES EXCLUDE REGEX ".*cocoa.*\\.m$")
list(FILTER XEMU_SOURCES EXCLUDE REGEX ".*os-win32\\.c$")

# Add Android-specific sources
set(ANDROID_SOURCES
    android-vulkan.c
    android-aaudio.c
    android-input.c
    android-file.c
    android-log.c
    android-jni.c
)

add_library(${CMAKE_PROJECT_NAME} SHARED
    ${XEMU_SOURCES}
    ${ANDROID_SOURCES}
)

# Link libraries
target_link_libraries(${CMAKE_PROJECT_NAME}
    android
    log
    vulkan
    aaudio
    # Add other dependencies as needed
)
