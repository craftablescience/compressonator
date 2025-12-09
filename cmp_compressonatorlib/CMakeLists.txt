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
    ${PROJECT_SOURCE_DIR}/applications/_plugins/common
    ${PROJECT_SOURCE_DIR}/applications/_libs/cmp_math/
)

target_include_directories(CMP_Compressonator
        PUBLIC
        ${PROJECT_SOURCE_DIR}/cmp_compressonatorlib
)

target_link_libraries(CMP_Compressonator
        PUBLIC
        CMP_Core
)

target_compile_definitions(CMP_Compressonator
        PRIVATE
        ENABLE_MAKE_COMPATIBLE_API  # Byte<->Float to make all source and dest compatible
)
