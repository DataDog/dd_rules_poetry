load("@remarshal_deps//:requirements.bzl", "requirement")

py_binary(
    name = "remarshal",
    srcs = ["remarshal.py"],
    visibility = ["//visibility:public"],
    deps = [
        requirement("PyYAML"),
        requirement("python-dateutil"),
        requirement("pytoml"),
        requirement("remarshal"),
        requirement("six"),
    ],
)