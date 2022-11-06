[![Build](https://github.com/acarg/cmake-deps-example-grpc/actions/workflows/main.yml/badge.svg)](https://github.com/acarg/cmake-deps-example-grpc/actions/workflows/main.yml)

# cmake-deps-example-grpc
An example showing how to handle dependencies with CMake, here using gRPC.
It goes with [this blog post](https://www.acarg.ch/posts/cmake-deps/).

This is building the [helloworld](https://github.com/grpc/grpc/tree/master/examples/cpp/helloworld)
example from gRPC.

## How to build

This project depends on gRPC, which has a few dependencies (listed
[here](dependencies/)).

### With a package manager

Because gRPC is available in `pacman` (the Arch package manager), it can
simply be used like this:

1. Install the dependencies:

    ```
    pacman -S grpc
    ```

2. Build the project

    ```
    cmake -Bbuild -S.
    cmake --build build
    ```

This is convenient on Arch, but this may not be the case on all platform.
Building the dependencies from sources becomes very interesting when
cross-compiling, for instance for Android.

### With the helper script

1. Build the dependencies and install them into `./dependencies/install`:

    ```
    cmake -DCMAKE_INSTALL_PREFIX=dependencies/install -DCMAKE_PREFIX_PATH=$(pwd)/dependencies/install -Bdependencies/build -Sdependencies
    cmake --build dependencies/build
    ```

Have a look into `dependencies/install`, and see that they were installed
_locally_. Now we can tell CMake to look there when using `find_package`,
by providing the path: `-DCMAKE_PREFIX_PATH=/path/to/dependencies/install`.

2. Build the project using the dependencies installed in step 1:

    ```
    cmake -DCMAKE_PREFIX_PATH=$(pwd)/dependencies/install -Bbuild -S.
    cmake --build build
    ```

## About the CI

The CI runs the helper script. That's very convenient because as a result,
it does not require a custom package manager. Also it gets very useful
for cross-compiling.

Finally, because the dependencies are installed locally, it's extremely
easy to [cache them](.github/workflows/main.yml#L24-L28):

```
- uses: actions/cache@v2
  id: cache
  with:
    path: ./install
    key: ${{ github.job }}-${{ hashFiles('./dependencies/**') }}
```

Here we tell the CI to cache all the files that are in `./install` at the
end of a successful build (note that `./install` is where the CI installs
them, instead of `./dependencies/install` above).

[Then](.github/workflows/main.yml#L30)
we can just tell the CI to not build the dependencies if they are
already in the cache:

```
if: steps.cache.outputs.cache-hit != 'true'
```
