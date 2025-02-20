load("//tensorflow:tensorflow.bzl", "if_google", "tf_cc_test")
load("//tensorflow/core/platform:rules_cc.bzl", "cc_library")

package(
    default_visibility = [":friends"],
    licenses = ["notice"],
)

package_group(
    name = "friends",
    packages = [
        # Authorized users go here.
        # copybara:uncomment "//learning/serving/...",
        # copybara:uncomment "//learning/brain/experimental/tfrt/...",
        "//tensorflow/core/tfrt/...",
        "//tensorflow/core/runtime_fallback/runtime/...",
    ],
)

cc_library(
    name = "run_handler_util",
    srcs = ["run_handler_util.cc"],
    hdrs = ["run_handler_util.h"],
    deps = ["//tensorflow/core:lib"],
)

tf_cc_test(
    name = "run_handler_util_test",
    size = "small",
    srcs = ["run_handler_util_test.cc"],
    deps = [
        ":run_handler_util",
        "//tensorflow/core:framework_internal",
        "//tensorflow/core:lib",
        "//tensorflow/core:test",
        "//tensorflow/core:test_main",
        "@com_google_googletest//:gtest_main",
    ],
)

cc_library(
    name = "run_handler",
    srcs = ["run_handler.cc"],
    hdrs = ["run_handler.h"],
    deps = [
        ":run_handler_util",
        "//tensorflow/core:framework",
        "//tensorflow/core:framework_internal",
        "//tensorflow/core:lib",
        "//tensorflow/core:lib_internal",
        "//tensorflow/core/profiler/lib:connected_traceme",
        "//tensorflow/core/profiler/lib:traceme",
        "//tensorflow/core/profiler/lib:traceme_encode",
        "//tensorflow/core/protobuf:for_core_protos_cc",
        "//tensorflow/core/tfrt/runtime:work_queue_interface",
        "//third_party/eigen3",
        "@tf_runtime//:hostcontext",
    ],
)

tf_cc_test(
    name = "run_handler_test",
    size = "small",
    srcs = ["run_handler_test.cc"],
    deps = [
        ":run_handler",
        "//tensorflow/core:framework_internal",
        "//tensorflow/core:lib",
        "//tensorflow/core:lib_internal",
        "//tensorflow/core:test",
        "//tensorflow/core:test_main",
        "//tensorflow/core/common_runtime:direct_session_internal",
        "//tensorflow/core/kernels:cwise_op",
        "//tensorflow/core/kernels:matmul_op",
        "//third_party/eigen3",
        "@com_google_absl//absl/memory",
        "@com_google_absl//absl/synchronization",
        "@com_google_googletest//:gtest_main",
        "@tf_runtime//:hostcontext",
    ],
)

cc_library(
    name = "run_handler_concurrent_work_queue",
    srcs = [
        "run_handler_concurrent_work_queue.cc",
    ] + if_google(
        ["//third_party/tf_runtime_eigen:concurrent_work_queue_hdrs"],
        ["@tf_runtime//third_party/concurrent_work_queue:concurrent_work_queue_hdrs"],
    ),
    hdrs = ["run_handler_concurrent_work_queue.h"],
    deps = [
        ":run_handler",
        "//tensorflow/core/platform:strcat",
        "//tensorflow/core/tfrt/runtime:work_queue_interface",
        "@llvm-project//llvm:Support",
        "@tf_runtime//:hostcontext",
        "@tf_runtime//:support",
    ],
)

tf_cc_test(
    name = "run_handler_concurrent_work_queue_test",
    srcs = ["run_handler_concurrent_work_queue_test.cc"],
    deps = [
        ":run_handler_concurrent_work_queue",
        "//tensorflow/core:framework_internal",
        "//tensorflow/core:lib",
        "//tensorflow/core:protos_all_cc",
        "//tensorflow/core:test_main",
        "//tensorflow/core/platform:errors",
        "//tensorflow/core/platform:status_matchers",
        "@com_google_absl//absl/strings",
        "@com_google_absl//absl/time",
        "@com_google_googletest//:gtest_main",
        "@tf_runtime//:hostcontext",
        "@tf_runtime//:support",
    ],
)
