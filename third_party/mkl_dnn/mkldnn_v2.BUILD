exports_files(["LICENSE"])

load(
    "@org_tensorflow//tensorflow:tensorflow.bzl",
    "tf_openmp_copts",
)
load(
    "@org_tensorflow//third_party/mkl_dnn:build_defs.bzl",
    "if_mkl_open_source_only",
    "if_mkldnn_threadpool",
)
load(
    "@org_tensorflow//third_party/mkl:build_defs.bzl",
    "if_mkl_ml",
)
load(
    "@org_tensorflow//third_party:common.bzl",
    "template_rule",
)

_DNNL_RUNTIME_OMP = {
    "#cmakedefine DNNL_CPU_THREADING_RUNTIME DNNL_RUNTIME_${DNNL_CPU_THREADING_RUNTIME}": "#define DNNL_CPU_THREADING_RUNTIME DNNL_RUNTIME_OMP",
    "#cmakedefine DNNL_CPU_RUNTIME DNNL_RUNTIME_${DNNL_CPU_RUNTIME}": "#define DNNL_CPU_RUNTIME DNNL_RUNTIME_OMP",
    "#cmakedefine DNNL_GPU_RUNTIME DNNL_RUNTIME_${DNNL_GPU_RUNTIME}": "#define DNNL_GPU_RUNTIME DNNL_RUNTIME_NONE",
    "#cmakedefine DNNL_USE_RT_OBJECTS_IN_PRIMITIVE_CACHE": "#undef DNNL_USE_RT_OBJECTS_IN_PRIMITIVE_CACHE",
    "#cmakedefine DNNL_WITH_SYCL": "#undef DNNL_WITH_SYCL",
    "#cmakedefine DNNL_WITH_LEVEL_ZERO": "#undef DNNL_WITH_LEVEL_ZERO",
    "#cmakedefine DNNL_SYCL_CUDA": "#undef DNNL_SYCL_CUDA",
}

_DNNL_RUNTIME_THREADPOOL = {
    "#cmakedefine DNNL_CPU_THREADING_RUNTIME DNNL_RUNTIME_${DNNL_CPU_THREADING_RUNTIME}": "#define DNNL_CPU_THREADING_RUNTIME DNNL_RUNTIME_THREADPOOL",
    "#cmakedefine DNNL_CPU_RUNTIME DNNL_RUNTIME_${DNNL_CPU_RUNTIME}": "#define DNNL_CPU_RUNTIME DNNL_RUNTIME_THREADPOOL",
    "#cmakedefine DNNL_GPU_RUNTIME DNNL_RUNTIME_${DNNL_GPU_RUNTIME}": "#define DNNL_GPU_RUNTIME DNNL_RUNTIME_NONE",
    "#cmakedefine DNNL_USE_RT_OBJECTS_IN_PRIMITIVE_CACHE": "#undef DNNL_USE_RT_OBJECTS_IN_PRIMITIVE_CACHE",
    "#cmakedefine DNNL_WITH_SYCL": "#undef DNNL_WITH_SYCL",
    "#cmakedefine DNNL_WITH_LEVEL_ZERO": "#undef DNNL_WITH_LEVEL_ZERO",
    "#cmakedefine DNNL_SYCL_CUDA": "#undef DNNL_SYCL_CUDA",
}

template_rule(
    name = "dnnl_config_h",
    src = "include/oneapi/dnnl/dnnl_config.h.in",
    out = "include/oneapi/dnnl/dnnl_config.h",
    substitutions = if_mkldnn_threadpool(
        _DNNL_RUNTIME_THREADPOOL,
        if_false = _DNNL_RUNTIME_OMP,
    ),
)

# Create the file mkldnn_version.h with MKL-DNN version numbers.
# Currently, the version numbers are hard coded here. If MKL-DNN is upgraded then
# the version numbers have to be updated manually. The version numbers can be
# obtained from the PROJECT_VERSION settings in CMakeLists.txt. The variable is
# set to "version_major.version_minor.version_patch". The git hash version can
# be set to NA.
# TODO(agramesh1) Automatically get the version numbers from CMakeLists.txt.

template_rule(
    name = "dnnl_version_h",
    src = "include/oneapi/dnnl/dnnl_version.h.in",
    out = "include/oneapi/dnnl/dnnl_version.h",
    substitutions = {
        "@DNNL_VERSION_MAJOR@": "2",
        "@DNNL_VERSION_MINOR@": "3",
        "@DNNL_VERSION_PATCH@": "2",
        "@DNNL_VERSION_HASH@": "N/A",
    },
)

cc_library(
    name = "mkl_dnn",
    srcs = glob([
        "src/common/*.cpp",
        "src/common/*.hpp",
        "src/common/ittnotify/*.c",
        "src/common/ittnotify/*.h",
        "src/cpu/*.cpp",
        "src/cpu/*.hpp",
        "src/cpu/gemm/*.cpp",
        "src/cpu/gemm/*.hpp",
        "src/cpu/gemm/f32/*",
        "src/cpu/gemm/s8x8s32/*",
        "src/cpu/matmul/*.cpp",
        "src/cpu/matmul/*.hpp",
        "src/cpu/reorder/*.cpp",
        "src/cpu/reorder/*.hpp",
        "src/cpu/rnn/*.cpp",
        "src/cpu/rnn/*.hpp",
        "src/cpu/loongarch64/*.h",
        "src/cpu/loongarch64/*.hpp",
        "src/cpu/loongarch64/*.cpp",
        "src/cpu/jit_utils/linux_perf/*.cpp",
        "src/cpu/jit_utils/linux_perf/*.hpp",
        "src/cpu/jit_utils/*.cpp",
        "src/cpu/jit_utils/*.hpp",
        "src/cpu/loongarch64/xbyak_loongarch64/src/*.cpp",
        "src/cpu/loongarch64/xbyak_loongarch64/xbyak_loongarch64/*.h",
        "src/cpu/loongarch64/injectors/*.cpp",
        "src/cpu/loongarch64/injectors/*.hpp",
        "src/cpu/loongarch64/gemm/f32/*.cpp",
        "src/cpu/loongarch64/gemm/f32/*.hpp",
        "src/cpu/loongarch64/gemm/s8x8s32/*.cpp",
        "src/cpu/loongarch64/gemm/s8x8s32/*.hpp",
        "src/cpu/loongarch64/gemm/*.cpp",
        "src/cpu/loongarch64/gemm/*.hpp",
        "src/cpu/loongarch64/utils/*.cpp",
        "src/cpu/loongarch64/utils/*.hpp",
        "src/cpu/loongarch64/brgemm/*.hpp",
        #"src/cpu/**/*.cpp",
        #"src/cpu/**/*.hpp",
        #"src/cpu/xbyak/*.h",
        #"src/cpu/x64/jit_utils/jitprofiling/*.c",
        #"src/cpu/x64/jit_utils/jitprofiling/*.h",
    ]) + [
        ":dnnl_config_h",
        ":dnnl_version_h",
    ],
    hdrs = glob(["include/*"]),
    copts = [
        "-fexceptions",
        "-UUSE_MKL",
        "-UUSE_CBLAS",
        "-UDNNL_X64=0",
        "-DDNNL_LOONGARCH64=1",
    ] + tf_openmp_copts(),
    includes = [
        "include",
        "src",
        "src/common",
        "src/cpu",
        "src/cpu/gemm",
        #"src/cpu/xbyak",
        "src/cpu/loongarch64/",
        "src/cpu/loongarch64/xbyak_loongarch64/xbyak_loongarch64",
    ],
    linkopts = ["-lgomp"],
    visibility = ["//visibility:public"],
    deps = if_mkl_ml(
        ["@org_tensorflow//third_party/mkl:intel_binary_blob"],
        [],
    ),
)

cc_library(
    name = "mkldnn_single_threaded",
    srcs = glob([
        "src/common/*.cpp",
        "src/common/*.hpp",
        "src/common/ittnotify/*.c",
        "src/common/ittnotify/*.h",
        "src/cpu/*.cpp",
        "src/cpu/*.hpp",
        "src/cpu/**/*.cpp",
        "src/cpu/**/*.hpp",
        "src/cpu/loongarch64/*.h",
        "src/cpu/loongarch64/*.hpp",
        "src/cpu/loongarch64/*.cpp",
        "src/cpu/jit_utils/linux_perf/*.cpp",
        "src/cpu/jit_utils/linux_perf/*.hpp",
        "src/cpu/jit_utils/*.cpp",
        "src/cpu/jit_utils/*.hpp",
        "src/cpu/loongarch64/xbyak_loongarch64/src/*.cpp",
        "src/cpu/loongarch64/xbyak_loongarch64/xbyak_loongarch64/*.h",
        "src/cpu/loongarch64/injectors/*.cpp",
        "src/cpu/loongarch64/injectors/*.hpp",
        "src/cpu/loongarch64/gemm/f32/*.cpp",
        "src/cpu/loongarch64/gemm/f32/*.hpp",
        "src/cpu/loongarch64/gemm/s8x8s32/*.cpp",
        "src/cpu/loongarch64/gemm/s8x8s32/*.hpp",
        "src/cpu/loongarch64/gemm/*.cpp",
        "src/cpu/loongarch64/gemm/*.hpp",
        "src/cpu/loongarch64/utils/*.cpp",
        "src/cpu/loongarch64/utils/*.hpp",
        "src/cpu/loongarch64/brgemm/*.hpp",
        #"src/cpu/xbyak/*.h",
    ]) + [":dnnl_config_h"],
    hdrs = glob(["include/*"]),
    copts = [
        "-fexceptions",
        "-DMKLDNN_THR=MKLDNN_THR_SEQ",  # Disables threading.
    ],
    includes = [
        "include",
        "src",
        "src/common",
        "src/cpu",
        "src/cpu/gemm",
        #"src/cpu/xbyak",
        "src/cpu/loongarch64/",
        "src/cpu/loongarch64/xbyak_loongarch64/xbyak_loongarch64",
    ],
    visibility = ["//visibility:public"],
)

cc_library(
    name = "mkl_dnn_aarch64",
    srcs = glob([
        "src/common/*.cpp",
        "src/common/*.hpp",
        "src/cpu/*.cpp",
        "src/cpu/*.hpp",
        "src/cpu/rnn/*.cpp",
        "src/cpu/rnn/*.hpp",
        "src/cpu/matmul/*.cpp",
        "src/cpu/matmul/*.hpp",
        "src/cpu/gemm/**/*",
    ]) + [
        ":dnnl_config_h",
        ":dnnl_version_h",
    ],
    hdrs = glob(["include/*"]),
    copts = [
        "-fexceptions",
        "-UUSE_MKL",
        "-UUSE_CBLAS",
    ],
    includes = [
        "include",
        "src",
        "src/common",
        "src/cpu",
        "src/cpu/gemm",
    ],
    linkopts = ["-lgomp"],
    visibility = ["//visibility:public"],
)
