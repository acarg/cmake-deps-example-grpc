cmake_minimum_required(VERSION 3.1)

project(superbuild CXX)

include(ExternalProject)

add_subdirectory(dependencies)

list(APPEND CMAKE_ARGS
    "-DCMAKE_PREFIX_PATH:PATH=${CMAKE_PREFIX_PATH}"
    "-DCMAKE_INSTALL_PREFIX:PATH=${CMAKE_INSTALL_PREFIX}"
    "-DCMAKE_TOOLCHAIN_FILE:PATH=${CMAKE_TOOLCHAIN_FILE}"
    "-DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE}"
    "-DCMAKE_POSITION_INDEPENDENT_CODE=ON"
    )

ExternalProject_Add(
    main
    URL "${CMAKE_CURRENT_SOURCE_DIR}/main"
    PREFIX main
    CMAKE_ARGS "${CMAKE_ARGS}"
    INSTALL_COMMAND ""
    DEPENDS grpc
)

