cmake_minimum_required(VERSION 3.10)

file(GLOB_RECURSE CMP_SRCS
    # Lossy Compression
    "apc/*.h"
    "apc/*.cpp"
    "atc/*.h"
    "atc/*.cpp"
    "ati/*.h"
    "ati/*.cpp"
    "ati/*.c"
    "basis/*.h"
    "basis/*.cpp"
    "bc6h/*.h"
    "bc6h/*.cpp"
    "bc7/*.h"
    "bc7/*.cpp"
    "block/*.h"
    "block/*.cpp"
    "buffer/*.h"
    "buffer/*.cpp"
    "dxt/*.h"
    "dxt/*.cpp"
    "dxtc/*.h"
    "dxtc/*.cpp"
    "dxtc/*.c"
    "etc/*.h"
    "etc/*.cpp"
    "etc/etcpack/*.h"
    "etc/etcpack/*.cpp"
    "etc/etcpack/*.cxx"
    "gt/*.h"
    "gt/*.cpp"

    # Common 
    "common/*.h"
    "common/*.cpp"
    ${PROJECT_SOURCE_DIR}/cmp_framework/common/*.h
    ${PROJECT_SOURCE_DIR}/cmp_framework/common/*.cpp
    ${PROJECT_SOURCE_DIR}/cmp_framework/common/half/*.h
    ${PROJECT_SOURCE_DIR}/cmp_framework/common/half/*.cpp
)

add_library(CMP_Compressonator 
    STATIC
    cmp_common.h
    compress.cpp
    compressonator.h
    compressonator.cpp
    ${CMP_SRCS}
)

target_include_directories(CMP_Compressonator
    PRIVATE
    ./
    # Lossy Compression
    apc
    atc
    ati
    basis
    bc6h
    bc7
    block
    buffer
    dxt
    dxtc
    etc
    etc/etcpack/
    gt

    # Common
    common
    ${PROJECT_SOURCE_DIR}/cmp_framework/common
    ${PROJECT_SOURCE_DIR}/cmp_framework/common/half
)

target_include_directories(CMP_Compressonator
        PUBLIC
        ${PROJECT_SOURCE_DIR}/cmp_compressonatorlib
)

target_link_libraries(CMP_Compressonator
        PUBLIC
        CMP_Core
)

# Set RPATH so the library can find its dependencies at runtime
# // https://duerrenberger.dev/blog/2021/08/04/understanding-rpath-with-cmake/
if(UNIX)
    set_target_properties(CMP_Compressonator PROPERTIES
        INSTALL_RPATH "$ORIGIN"
        BUILD_WITH_INSTALL_RPATH TRUE
    )
endif()
