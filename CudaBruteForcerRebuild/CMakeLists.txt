# Add new source files here
add_library(bruteforcer SHARED #ClInclude
        "BruteforceFunctions.cuh"
        "BruteforceVariables.cuh"
        "CommonFunctions.cuh"
        "Floors.cuh"
        "Platform.cu"
        #    "Platform.cuh"
        "PlatformSolutionFunctions.cuh"
        "RunParameters.cpp"
        "RunParameters.hpp"
        "SolutionStructs.cuh"
        "Surface.cpp"
        "Surface.hpp"
        "UpwarpSolutionFunctions.cuh"
        "vmath.cpp"
        "vmath.hpp"
        "BoundaryDistance.cpp"
        "BoundaryDistance.hpp"
        "Path.cpp"
        "Path.hpp"
        "Trig.cpp"
        "Trig.hpp"
        )

target_compile_features(bruteforcer PRIVATE cuda_std_14)
set_target_properties( bruteforcer
        PROPERTIES
        CUDA_SEPARABLE_COMPILATION ON
        CUDA_RUNTIME_LIBRARY Shared
        CUDA_ARCHITECTURES "75")

add_executable(main
        "BruteforceFunctions.cu"
        "BruteforceVariables.cu"
        "PlatformSolutionFunctions.cu"
        "Floors.cu"
        "main.cu"
        "CommonFunctions.cu"
        "SolutionStructs.cu"
        "UpwarpSolutionFunctions.cu"
        )

set_target_properties(main PROPERTIES
        RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}
        PDB_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}
        CUDA_SEPARABLE_COMPILATION ON
        CUDA_ARCHITECTURES "75"
        )
# the following line shouldn't be necessary as target_link_libraries
# with OpenMP::OpenMP_CXX should pass the correct flags for OpenMP
# but apparently, this is KO when using nvcc :(
# You can remove that if OpenMP is not required
# checked using cmake 3.14.3 (October 2019)

target_compile_options(main PUBLIC
        $<$<COMPILE_LANGUAGE:CXX>:${OpenMP_CXX_FLAGS}>
        $<$<COMPILE_LANGUAGE:CUDA>:-Xcompiler ${OpenMP_CXX_FLAGS}>
        )

target_link_libraries(main PUBLIC CUDA::cublas)
target_link_libraries(main PUBLIC OpenMP::OpenMP_CXX)

target_link_libraries(main PRIVATE bruteforcer)