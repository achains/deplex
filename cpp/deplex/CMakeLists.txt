#####################################
# Define Target Information
#####################################
set(TARGET_NAME deplex)

set(TARGET_INCLUDE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/include)
set(TARGET_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src)

set(SRC_FILES
        ${TARGET_SOURCE_DIR}/deplex/config.cpp
        ${TARGET_SOURCE_DIR}/deplex/cell_segment_stat.cpp
        ${TARGET_SOURCE_DIR}/deplex/cell_segment.cpp
        ${TARGET_SOURCE_DIR}/deplex/cell_grid.cpp
        ${TARGET_SOURCE_DIR}/deplex/normals_histogram.cpp
        ${TARGET_SOURCE_DIR}/deplex/plane_extractor.cpp
        ${TARGET_SOURCE_DIR}/deplex/utils/eigen_io.cpp
        ${TARGET_SOURCE_DIR}/deplex/utils/depth_image.cpp
        )
#####################################
# Required packages
#####################################
find_package(Eigen3 REQUIRED)

#####################################
# Optional packages
#####################################
if (EXPERIMENTAL_OPENMP)
    find_package(OpenMP)
    if (NOT OpenMP_CXX_FOUND)
        message(WARNING "Flag EXPERIMENTAL_OPENMP is ON, but couldn't find OpenMP")
    endif ()
endif ()

#####################################
# Define Target
#####################################
add_library(${TARGET_NAME} ${SRC_FILES})

#####################################
# Define Target Properties
#####################################
set_property(TARGET ${TARGET_NAME} PROPERTY CXX_STANDARD 14)
target_compile_features(${TARGET_NAME} PUBLIC cxx_std_14)
if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
    target_compile_options(${TARGET_NAME} PRIVATE -stdlib=libc++)
endif ()

if (MSVC)
    target_compile_definitions(${TARGET_NAME} PUBLIC _USE_MATH_DEFINES)
endif ()

if (DEBUG_DEPLEX)
    target_compile_definitions(${TARGET_NAME} PRIVATE DEBUG_DEPLEX)
endif ()

if (BENCHMARK_LOGGING)
    target_compile_definitions(${TARGET_NAME} PRIVATE BENCHMARK_LOGGING)
endif ()

if (DEBUG_BENCHMARK)
    message("Disabling optimizations")
    target_compile_options(${TARGET_NAME} PRIVATE -pg -O0)
endif ()

target_include_directories(
        ${TARGET_NAME}
        SYSTEM
        PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
)

target_link_libraries(${TARGET_NAME} PUBLIC Eigen3::Eigen)
target_link_libraries(${TARGET_NAME} PRIVATE dsyev)
target_link_libraries(${TARGET_NAME} PRIVATE rtl)
target_link_libraries(${TARGET_NAME} PUBLIC mimalloc)

if (OpenMP_CXX_FOUND)
    target_link_libraries(${TARGET_NAME} PRIVATE OpenMP::OpenMP_CXX)
endif ()

set_target_properties(${TARGET_NAME} PROPERTIES
        LIBRARY_OUTPUT_DIRECTORY "${DEPLEX_LIB_DIR}")
