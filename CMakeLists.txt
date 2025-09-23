cmake_minimum_required(VERSION 3.10)

if(POLICY CMP0091)
   cmake_policy(SET CMP0091 NEW)
endif()

if(POLICY CMP0076)
  cmake_policy(SET CMP0076 NEW)
endif()

# Setting version information

if (NOT CMP_VERSION_MAJOR)
    set(CMP_VERSION_MAJOR 5)
endif()

if (NOT CMP_VERSION_MINOR)
    set(CMP_VERSION_MINOR 0)
endif()

if (NOT CMP_VERSION_BUILD_NUMBER)
    set(CMP_VERSION_BUILD_NUMBER 0)
endif()

project(Compressonator VERSION ${CMP_VERSION_MAJOR}.${CMP_VERSION_MINOR})
message("Building Compressonator version ${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}.${PROJECT_VERSION_PATCH}")

# Helper function for the build

include(cmake/helperfunctions.cmake)

# Common compiler options

if (CMP_HOST_WINDOWS)
    add_compile_options(/W4 /wd4201)
#    add_compile_options(/W4 /WX /wd4201)
elseif(CMP_HOST_LINUX)
    add_compile_options(-Wall)
#    add_compile_options(-Wall -Werror)
endif()

# Set compiler feature support levels
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(COMPILER_SUPPORTS_CXX11 ON)
set(COMPILER_SUPPORTS_CXX14 ON)
set(COMPILER_SUPPORTS_CXX17 ON)
add_compile_definitions(_CMP_CPP17_=1)

# -----------------------------------------------------------------------------------
# Code build definitions and features: These options are been revised for v4.2
# -----------------------------------------------------------------------------------
add_compile_definitions(
    USE_ETCPACK                 # Use ETCPack for ETC2 else use CModel code!
    ENABLE_MAKE_COMPATIBLE_API  # Byte<->Float to make all source and dest compatible
    USE_OLD_SWIZZLE             # Remove swizzle flag and abide by CMP_Formats
)

# '_DEBUG' is only defined by Visual Studio. Make sure it's defined everywhere
add_compile_definitions($<$<CONFIG:Debug>:_DEBUG>)

if (CMP_HOST_WINDOWS)
    add_compile_definitions(_WIN32)
    set(CMAKE_EXE_LINKER_FLAGS "/INCREMENTAL:NO")
else()
    add_definitions(-fPIC -Wno-write-strings)
    find_package(Threads REQUIRED)
endif()

# These are the minimum build includes for both Windows / Linux / Mac application builds
# Libs common to all OS
add_subdirectory(cmp_core)
add_subdirectory(cmp_compressonatorlib)
