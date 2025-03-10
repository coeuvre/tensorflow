load("@llvm-project//mlir:tblgen.bzl", "gentbl_cc_library")
load("//tensorflow:tensorflow.bzl", "get_compatible_with_cloud")
load("//tensorflow:tensorflow.bzl", "if_google")

# TF to TFRT kernels conversion.
package(
    licenses = ["notice"],
)

gentbl_cc_library(
    name = "GpuPassesIncGen",
    compatible_with = get_compatible_with_cloud(),
    tbl_outs = [(
        ["-gen-pass-decls"],
        "gpu_passes.h.inc",
    )],
    tblgen = "@llvm-project//mlir:mlir-tblgen",
    td_file = "gpu_passes.td",
    deps = [
        "@llvm-project//mlir:PassBaseTdFiles",
    ],
)

cc_library(
    name = "pattern_utils",
    srcs = ["pattern_utils.cc"],
    hdrs = ["pattern_utils.h"],
    tags = ["gpu"],
    deps = [
        "@llvm-project//mlir:IR",
        "@llvm-project//mlir:StandardOps",
        "@tf_runtime//:basic_kernels_opdefs",
        "@tf_runtime//backends/gpu:gpu_wrapper",
    ],
)

cc_library(
    name = "lmhlo_to_gpu",
    srcs = [
        "ccl_pattern.cc",
        "cholesky_pattern.cc",
        "custom_call_pattern.cc",
        "gemm_pattern.cc",
        "lmhlo_to_gpu.cc",
        "triangular_solve_pattern.cc",
    ],
    hdrs = [
        "lmhlo_to_gpu.h",
    ],
    tags = [
        "gpu",
        "no_oss",
    ],
    visibility = if_google([
        "//platforms/xla/tests/gpu:__pkg__",
    ]) + [
        "//tensorflow/compiler/mlir/tfrt:__pkg__",
        "//tensorflow/compiler/xla/service/gpu:__pkg__",
    ],
    deps = [
        ":GpuPassesIncGen",
        ":pattern_utils",
        "//tensorflow/compiler/mlir/hlo:lhlo",
        "//tensorflow/compiler/mlir/hlo:lhlo_gpu",
        "//tensorflow/compiler/mlir/xla:attribute_exporter",
        "//tensorflow/compiler/mlir/xla:type_to_shape",
        "//tensorflow/compiler/xla:shape_util",
        "//tensorflow/compiler/xla:util",
        "//tensorflow/compiler/xla:xla_data_proto_cc",
        "//tensorflow/compiler/xla/service/gpu:ir_emission_utils",
        "//tensorflow/compiler/xla/service/gpu:nccl_collective_thunks",
        "//tensorflow/compiler/xla/service/gpu:xlir_opdefs",
        "@llvm-project//llvm:Support",
        "@llvm-project//mlir:GPUDialect",
        "@llvm-project//mlir:IR",
        "@llvm-project//mlir:MemRefDialect",
        "@llvm-project//mlir:Pass",
        "@llvm-project//mlir:StandardOps",
        "@llvm-project//mlir:StandardOpsTransforms",
        "@llvm-project//mlir:Support",
        "@llvm-project//mlir:Transforms",
        "@tf_runtime//:basic_kernels_opdefs",
        "@tf_runtime//backends/gpu:gpu_opdefs",
        "@tf_runtime//backends/gpu:gpu_passes",
        "@tf_runtime//backends/gpu:gpu_wrapper",
    ],
    alwayslink = 1,
)

# The lmhlo-to-gpu-binary pass is a separate target to avoid a dependency cycle:
# :lmhlo_to_gpu_binary > xla/service/gpu:gpu_executable > :lmhlo_to_gpu
cc_library(
    name = "lmhlo_to_gpu_binary",
    srcs = [
        "fusion_pattern.cc",
        "lmhlo_to_gpu_binary.cc",
    ],
    hdrs = [
        "lmhlo_to_gpu_binary.h",
    ],
    tags = ["gpu"],
    visibility = if_google([
        "//platforms/xla/tests/gpu:__pkg__",
    ]) + [
        "//tensorflow/compiler/mlir/tfrt:__pkg__",
        "//tensorflow/compiler/xla/service/gpu:__pkg__",
    ],
    deps = [
        ":GpuPassesIncGen",
        "//tensorflow/compiler/mlir/hlo:lhlo",
        "//tensorflow/compiler/mlir/hlo:lhlo_gpu",
        "//tensorflow/compiler/mlir/tensorflow",
        "//tensorflow/compiler/mlir/xla:hlo_utils",
        "//tensorflow/compiler/xla/service/gpu:buffer_allocations",
        "//tensorflow/compiler/xla/service/gpu:gpu_executable",
        "//tensorflow/compiler/xla/service/gpu:ir_emitter",
        "//tensorflow/compiler/xla/service/gpu:launch_dimensions",
        "//tensorflow/compiler/xla/service/gpu:nvptx_helper",
        "//tensorflow/compiler/xla/service/gpu:thunk",
        "//tensorflow/compiler/xla/service/gpu/llvm_gpu_backend",
        "@llvm-project//llvm:Support",
        "@llvm-project//mlir:ArithmeticDialect",
        "@llvm-project//mlir:GPUDialect",
        "@llvm-project//mlir:IR",
        "@llvm-project//mlir:MemRefDialect",
        "@llvm-project//mlir:Pass",
        "@llvm-project//mlir:StandardOps",
        "@llvm-project//mlir:Support",
        "@llvm-project//mlir:Transforms",
        "@tf_runtime//backends/gpu:gpu_passes",
    ],
    alwayslink = 1,
)
