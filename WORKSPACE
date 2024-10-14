workspace(
    name = "com_sonia_rules_poetry",
)

load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")

# Bazel Python rules (https://github.com/bazelbuild/rules_python/releases)
# Note that we purposedly use an older version than latest, as release 0.28.0 drops Bazel 5 compatibility.
SHA="a5640fddd4beb03e8c1fde5ed7160c0ba6bd477e7d048661c30c06936a26fd63"
VERSION="0.22.1"

http_archive(
    name = "rules_python",
    sha256 = SHA,
    strip_prefix = "rules_python-{}".format(VERSION),
    url = "https://github.com/bazelbuild/rules_python/releases/download/{}/rules_python-{}.tar.gz".format(VERSION,VERSION),
)

load("@rules_python//python:repositories.bzl", "py_repositories", "python_register_toolchains")
py_repositories()

# Python toolchain
load("@rules_python//python:versions.bzl", "MINOR_MAPPING")
python_register_toolchains(
    name = "python_3_11",
    ignore_root_user_error = True,  # avoids error in Gitlab jobs
    python_version = MINOR_MAPPING["3.11"],
)

# Pip rules
load("@rules_python//python:pip.bzl", "pip_parse")
load("@python_3_11//:defs.bzl", python_interpreter = "interpreter")

# install pip and setuptools locally
load("@com_sonia_rules_poetry//rules_poetry:defs.bzl", "poetry_deps")

poetry_deps()



# Go rules to make the Docker rules work

http_archive(
  name = "io_bazel_rules_go",
  sha256 = "099a9fb96a376ccbbb7d291ed4ecbdfd42f6bc822ab77ae6f1b5cb9e914e94fa",
  urls = [
    "https://mirror.bazel.build/github.com/bazelbuild/rules_go/releases/download/v0.35.0/rules_go-v0.35.0.zip",
    "https://github.com/bazelbuild/rules_go/releases/download/v0.35.0/rules_go-v0.35.0.zip",
  ],
)

load("@io_bazel_rules_go//go:deps.bzl", "go_register_toolchains", "go_rules_dependencies")

go_rules_dependencies()

go_register_toolchains(version = "1.19.1")



# Docker rules for testing

http_archive(
    name = "io_bazel_rules_docker",
    sha256 = "b1e80761a8a8243d03ebca8845e9cc1ba6c82ce7c5179ce2b295cd36f7e394bf",
    urls = ["https://github.com/bazelbuild/rules_docker/releases/download/v0.25.0/rules_docker-v0.25.0.tar.gz"],
)

load(
    "@io_bazel_rules_docker//repositories:repositories.bzl",
    _container_repositories = "repositories",
)

_container_repositories()

load("@io_bazel_rules_docker//repositories:deps.bzl", _container_deps = "deps")

_container_deps()

pip_parse(
    name = "remarshal_deps",
    requirements = "//tools:requirements.txt",
    python_interpreter_target = python_interpreter,
)

load("@remarshal_deps//:requirements.bzl", remarshal_install_deps = "install_deps")

remarshal_install_deps()

# Poetry rules
load("//rules_poetry:poetry.bzl", "poetry")

poetry(
    name = "test_poetry",
    excludes = [
        "enum34",
        "setuptools",
    ],
    lockfile = "//test:poetry.lock",
    pyproject = "//test:pyproject.toml",
    python_interpreter_target = python_interpreter,
)

poetry(
    name = "test_poetry_no_groups",
    excludes = [
        "enum34",
        "setuptools",
    ],
    lockfile = "//test/no_group_deps:poetry.lock",
    pyproject = "//test/no_group_deps:pyproject.toml",
    python_interpreter_target = python_interpreter,
)



# Base image for Docker tests
load(
    "@io_bazel_rules_docker//container:container.bzl",
    "container_pull",
)

container_pull(
    name = "python_debian",
    digest = "sha256:fc754aafacf5ad737f1e313cbd3f7cfedf08cbc713927a9e27683b7210a0aabd",
    registry = "index.docker.io",
    repository = "library/python",
    tag = "3.7.4-slim-buster",
)
