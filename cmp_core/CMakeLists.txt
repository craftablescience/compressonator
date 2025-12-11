add_library(CMP_Core STATIC
    shaders/bc1_encode_kernel.h
    shaders/bc1_common_kernel.h
    shaders/bc1_encode_kernel.cpp
    shaders/bc2_encode_kernel.h
    shaders/bc2_encode_kernel.cpp
    shaders/bc3_encode_kernel.h
    shaders/bc3_encode_kernel.cpp
    shaders/bc4_encode_kernel.h
    shaders/bc4_encode_kernel.cpp
    shaders/bc5_encode_kernel.h
    shaders/bc5_encode_kernel.cpp
    shaders/bc6_encode_kernel.h
    shaders/bc6_encode_kernel.cpp
    shaders/bc7_encode_kernel.h
    shaders/bc7_common_encoder.h
    shaders/bc7_encode_kernel.cpp
    shaders/bcn_common_kernel.h
    shaders/bcn_common_api.h
    shaders/common_def.h

    source/cmp_core.h
    source/cmp_core.cpp
    source/cmp_math_common.cpp
    source/cmp_math_vec4.h
    source/cmp_math_func.h
    source/cpu_extensions.cpp
)

target_include_directories(CMP_Core
    PUBLIC
    shaders
    source
)

if (UNIX)
    target_compile_definitions(CMP_Core PRIVATE _LINUX ASPM_GPU)
endif()

# Core SIMD options

if(APPLE)
    # Send me a patch that fixes these if you want SIMD because I do not care
    target_sources(CMP_Core
            PRIVATE
            source/core_simd_apple.cpp
    )
else()
    # Check architecture
    include(CheckCXXSourceCompiles)
    check_cxx_source_compiles("
    #if defined(__aarch64__) || defined(__arm__)
    int main() { return 0; }
    #else
    #error not arm64
    #endif
    " CMP_IS_ARM64)

    # SSE
    add_library(CMP_Core_SSE STATIC)
    target_sources(CMP_Core_SSE PRIVATE source/core_simd_sse.cpp)
    target_include_directories(CMP_Core_SSE PRIVATE source shaders)

    if (UNIX OR MINGW)
        if (CMP_IS_ARM64)
            target_compile_definitions(CMP_Core_SSE PRIVATE CMP_ARM64_BUILD)
        else()
            target_compile_options(CMP_Core_SSE PRIVATE -msse4.1)
        endif()
    endif()

    # AVX
    add_library(CMP_Core_AVX STATIC)
    target_sources(CMP_Core_AVX PRIVATE source/core_simd_avx.cpp)
    target_include_directories(CMP_Core_AVX PRIVATE source shaders)

    if (WIN32)
        if (CMP_IS_ARM64)
            target_compile_definitions(CMP_Core_AVX PRIVATE CMP_ARM64_BUILD)
        elseif(MSVC)
            target_compile_options(CMP_Core_AVX PRIVATE /arch:AVX2)
        else()
            target_compile_options(CMP_Core_AVX PRIVATE -mavx2)
        endif()
    else()
        if (CMP_IS_ARM64)
            target_compile_definitions(CMP_Core_AVX PRIVATE CMP_ARM64_BUILD)
        else()
            target_compile_options(CMP_Core_AVX PRIVATE -mavx2)
        endif()
    endif()

    # AVX-512
    add_library(CMP_Core_AVX512 STATIC)
    target_sources(CMP_Core_AVX512 PRIVATE source/core_simd_avx512.cpp)
    target_include_directories(CMP_Core_AVX512 PRIVATE source shaders)

    if (WIN32)
        if (CMP_IS_ARM64)
            target_compile_definitions(CMP_Core_AVX512 PRIVATE CMP_ARM64_BUILD)
        elseif(MSVC)
            target_compile_options(CMP_Core_AVX512 PRIVATE /arch:AVX512)
        else()
            target_compile_options(CMP_Core_AVX512 PRIVATE -mavx512f)
        endif()
    else()
        if (CMP_IS_ARM64)
            target_compile_definitions(CMP_Core_AVX512 PRIVATE CMP_ARM64_BUILD)
        else()
            target_compile_options(CMP_Core_AVX512 PRIVATE -mavx512f)
        endif()
    endif()

    # Link SIMD libraries to CMP_Core
    target_link_libraries(CMP_Core PUBLIC CMP_Core_SSE CMP_Core_AVX CMP_Core_AVX512)
endif()
