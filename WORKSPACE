load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")

# gRPC.
http_archive(
    name = "com_github_grpc_grpc",
#    sha256 = "d6af0859d3ae4693b1955e972aa2e590d6f4d44baaa82651467c6beea453e30e",
    strip_prefix = "grpc-1.25.0", #"6.0-pre1",
    urls = [
        "https://github.com/grpc/grpc/archive/v1.25.0.tar.gz" #"6.0-pre1.tar.gz",
    ],
)
http_archive(
    name = "com_google_protobuf",
    sha256 = "7adbf4833bc56e201db3076e864f6f4fd3043b5895e5f7e6ab953d385b49a926",
    strip_prefix = "protobuf-fe1790ca0df67173702f70d5646b82f48f412b99",
    url = "https://github.com/protocolbuffers/protobuf/archive/fe1790ca0df67173702f70d5646b82f48f412b99.tar.gz"
)

# Pull in all gRPC dependencies.
load("@com_github_grpc_grpc//bazel:grpc_deps.bzl", "grpc_deps")
load(":grpc_deps.bzl", "grpc_deps")

grpc_deps()
#load("@com_github_grpc_grpc//bazel:grpc_extra_deps.bzl", "grpc_extra_deps")
load(":grpc_extra_deps.bzl", "grpc_extra_deps")
grpc_extra_deps()
