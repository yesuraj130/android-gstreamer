cmake_minimum_required(VERSION 3.22.1)
project("gst_pipeline_runner" C CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Locate GStreamer Android SDK root directory
if(NOT DEFINED GSTREAMER_ROOT_ANDROID OR "${GSTREAMER_ROOT_ANDROID}" STREQUAL "")
    if(DEFINED ENV{GSTREAMER_ROOT_ANDROID})
        set(GSTREAMER_ROOT_ANDROID $ENV{GSTREAMER_ROOT_ANDROID})
    else()
        set(GSTREAMER_ROOT_ANDROID "/opt/gstreamer-android")
    endif()
endif()

set(GST_ARCH_DIR "${GSTREAMER_ROOT_ANDROID}/${ANDROID_ABI}")
message(STATUS "GStreamer Android SDK ABI path: ${GST_ARCH_DIR}")

# Include paths for GStreamer 1.0, GLib, and GObject
include_directories(
    ${GST_ARCH_DIR}/include/gstreamer-1.0
    ${GST_ARCH_DIR}/include/glib-2.0
    ${GST_ARCH_DIR}/lib/glib-2.0/include
    ${GST_ARCH_DIR}/include
    ${CMAKE_CURRENT_SOURCE_DIR}
)

link_directories(
    ${GST_ARCH_DIR}/lib
    ${GST_ARCH_DIR}/lib/gstreamer-1.0
)

# Core native shared library
add_library(gst_pipeline_runner SHARED
    gst_pipeline_runner.cpp
)

# Standard Android system libraries
find_library(log-lib log)
find_library(android-lib android)

# Link GStreamer, RTSP server, and Android NDK libraries
target_link_libraries(gst_pipeline_runner
    ${log-lib}
    ${android-lib}
    -L${GST_ARCH_DIR}/lib
    gstreamer-1.0
    gobject-2.0
    glib-2.0
    gstvideo-1.0
    gstrtp-1.0
    gstrtsp-1.0
    gstrtspserver-1.0
    gstapp-1.0
)
