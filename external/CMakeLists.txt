add_subdirectory(eigen3)
add_subdirectory(mimalloc)

if (${BUILD_TESTS})
    add_subdirectory(googletest)
endif()

if (${BUILD_BENCHMARK})
    add_subdirectory(benchmark)
endif()

if (${BUILD_PYTHON})
    add_subdirectory(pybind11)
endif()