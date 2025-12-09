cmake_minimum_required(VERSION 3.16)

project(Compressonator VERSION 5.0.0)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# -----------------------------------------------------------------------------------
# Code build definitions and features: These options are been revised for v4.2
# -----------------------------------------------------------------------------------
add_compile_definitions(
    USE_ETCPACK                 # Use ETCPack for ETC2 else use CModel code!
    ENABLE_MAKE_COMPATIBLE_API  # Byte<->Float to make all source and dest compatible
)

# '_DEBUG' is only defined by Visual Studio. Make sure it's defined everywhere
add_compile_definitions($<$<CONFIG:Debug>:_DEBUG>)

if (NOT WIN32)
    find_package(Threads REQUIRED)
endif()

# These are the minimum build includes for both Windows / Linux / Mac application builds
# Libs common to all OS
add_subdirectory(cmp_core)
add_subdirectory(cmp_compressonatorlib)
