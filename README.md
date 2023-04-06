[![Build](https://github.com/acarg/cmake-deps-example-grpc-superbuild/actions/workflows/main.yml/badge.svg)](https://github.com/acarg/cmake-deps-example-grpc-superbuild/actions/workflows/main.yml)

# cmake-deps-example-grpc-superbuild
An example showing a superbuild with CMake.
It goes with [this blog post](https://www.acarg.ch/posts/cmake-superbuild/).

This is building the [helloworld](https://github.com/grpc/grpc/tree/master/examples/cpp/helloworld)
example from gRPC.

## How to build

This project depends on gRPC, which has a few dependencies (listed
[here](dependencies/)). The dependencies will be built together with
the main project (as a "superbuild"). To see how to build the main
project separately, see [this other blog post](https://www.acarg.ch/posts/cmake-deps/).

To run the superbuild, just run:

```
cmake -DCMAKE_INSTALL_PREFIX=build/install -DCMAKE_PREFIX_PATH=$(pwd)/build/install -Bbuild -S.
cmake --build build
```

