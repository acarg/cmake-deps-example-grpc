[![Build](https://github.com/acarg/cmake-deps-example-grpc/actions/workflows/main.yml/badge.svg)](https://github.com/acarg/cmake-deps-example-grpc/actions/workflows/main.yml)

# cmake-deps-examples
An example showing how to handle dependencies with CMake, here using gRPC.

> Warning: This is not trying to do anything useful with gRPC! I just
chose it as an example because it is not trivial to build its dependencies.

## How to build

This project depends on gRPC, which has a few dependencies (listed
[here](dependencies/)).

### With a package manager

I wanted to show a "simple build" with a package manager, using Pacman
from ArchLinux. Proxygen is not in the default repo, but it is in the AUR.
That means that it could be installed e.g. using `yay`.

It should have been like this:

1. Install the dependencies:

    ```
    yay -S proxygen
    ```

2. Build the project

    ```
    cmake -Bbuild -S.
    cmake --build build
    ```

However, those packages are not super up-to-date on the AUR and fail to
build (at least `folly` and `wangle` fail, that's where I gave up). So
I failed installing Proxygen from there.

Which makes a point: if you decide to depend on a package manager, then...
you depend on it. In this case the user-maintained packages showed their
limits.

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
it does not result in a custom package manager. Also it gets very useful
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
