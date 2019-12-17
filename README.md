# repro-grpc-bazel

This repo is a small reproduction case of the following issue.

I am trying to build a grpc C++ library for the remote execution APIs.

I wrote the [build file](https://github.com/edbaunton/repro-grpc-bazel/blob/master/protos/BUILD.bazel) following all the examples online. But when I build I get:

```bash
$ bazel build //...
DEBUG: Rule 'com_github_grpc_grpc' indicated that a canonical reproducible form can be obtained by modifying arguments sha256 = "ffbe61269160ea745e487f79b0fd06b6edd3d50c6d9123f053b5634737cf2f69"
DEBUG: Call stack for the definition of repository 'com_github_grpc_grpc' which is a http_archive (rule definition at /private/var/tmp/_bazel_user1/f8d878f3ed4010ec5ff5a0f018f0d27b/external/bazel_tools/tools/build_defs/repo/http.bzl:292:16):
 - /Users/user1/repro-grpc-bazel/WORKSPACE:4:1
INFO: Analyzed 9 targets (0 packages loaded, 0 targets configured).
INFO: Found 9 targets...
ERROR: /Users/user1/repro-grpc-bazel/protos/BUILD.bazel:63:1: C++ compilation of rule '//protos:build_bazel_remote_execution_v2_remote_execution_cpp_grpc' failed (Exit 1) cc_wrapper.sh failed: error executing command external/local_config_cc/cc_wrapper.sh -U_FORTIFY_SOURCE -fstack-protector -Wall -Wthread-safety -Wself-assign -fcolor-diagnostics -fno-omit-frame-pointer '-std=c++0x' -MD -MF ... (remaining 55 argument(s) skipped)

Use --sandbox_debug to see verbose messages from the sandbox
bazel-out/darwin-fastbuild/bin/protos/_virtual_imports/build_bazel_remote_execution_v2_remote_execution_proto/build/bazel/remote/execution/v2/remote_execution.grpc.pb.cc:5:10: fatal error: 'protos/_virtual_imports/build_bazel_remote_execution_v2_remote_execution_proto/build/bazel/remote/execution/v2/remote_execution.pb.h' file not found
#include "protos/_virtual_imports/build_bazel_remote_execution_v2_remote_execution_proto/build/bazel/remote/execution/v2/remote_execution.pb.h"
         ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
1 error generated.
INFO: Elapsed time: 1.049s, Critical Path: 0.85s
INFO: 0 processes.
FAILED: Build did NOT complete successfully
```

That is, the final `build_bazel_remote_execution_v2_remote_execution_cpp_grpc` rule cannot
find the header that was generated during the protobuf rule that is actually passed in as a
dependency as `build_bazel_remote_execution_v2_remote_execution_cpp_proto`. What I did find
however is that if you add in `build_bazel_remote_execution_v2_remote_execution_cpp_proto` as
a dep to the `cc_library` in [here](https://github.com/edbaunton/repro-grpc-bazel/blob/master/protos/cc_grpc_library.bzl#L96) then the protobuf header is visible to the build of the grpc library.

So if you [uncomment line 2 and comment out line 3](https://github.com/edbaunton/repro-grpc-bazel/blob/master/protos/BUILD.bazel#L2-L3) to use my patched version you will find that the build works as we are pulling in the deps to the srcs and the header is visible.
