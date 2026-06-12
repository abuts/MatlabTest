cmake_minimum_required(VERSION 3.29)

set(MTEST_ROOT ${CMAKE_CURRENT_LIST_DIR})
file(READ "${MTEST_ROOT}/VERSION" _version)
string(STRIP "${_version}" _version)
project("MTEST_Proj" VERSION "${_version}")

#
cmake_host_system_information(RESULT CMAKE_HOST_SYSTEM_NAME QUERY OS_NAME)

# C++11 is required for GTest but 20 for some plugins
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED TRUE)

# Sort our targets into folders
set_property(GLOBAL PROPERTY USE_FOLDERS ON)

# Set our options
option(BUILD_TESTS "Build the C++ tests" OFF)
# to control CPP check warnings and enhance analysis
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)


# Add cmake directory to CMake's path
list(APPEND CMAKE_MODULE_PATH "${MTEST_ROOT}/cmake"
     "${MTEST_ROOT}/cmake/external" "${MTEST_ROOT}/cmake/shared")

# This sets the destination for mex build artefacts (used in PACE_AddMex)
IF(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
   # Mac OS X specific code
   SET(Matlab_ARCH "_MACI64")
   option(USE_HORACE_MPI "Use MPI libraries provided with Horace" OFF)
ELSEIF(${CMAKE_SYSTEM_NAME} MATCHES "Linux")
   SET(Matlab_ARCH "_GLNXA64")
   option(USE_HORACE_MPI "Use MPI libraries provided with Horace" ON)
ELSEIF (${CMAKE_SYSTEM_NAME} MATCHES "Windows")
   SET(Matlab_ARCH "_PCWIN64")
   option(USE_HORACE_MPI "Use MPI libraries provided with Horace" ON)
ELSE()
   message(FATAL_ERROR "OS name ${CMAKE_HOST_SYSTEM_NAME} have not been recognized. HORACE have never been build on such system" )
ENDIF()
#set(Horace_DLL_DIRECTORY "${Horace_ROOT}/horace_core/DLL/${Matlab_ARCH}")

# Look for packages early so we can exit if they're not found
find_package(OpenMP)
include(PACE_FindMatlab)
#
message(" MATLAB CURRENT VERSION: ${MATLAB_CURRENT_VERSION}; Used Release: ${Matlab_RELEASE}")

include(PACE_Version)
include(PACE_AddMex)
include(PACE_AddCppUnitTest)

#include(herbert_FindMPI)
include(CTest)
if(BUILD_TESTING)
    include(PACE_FindGTest)
    enable_testing()
endif()

#add_subdirectory("_LowLevelCode")
#if(BUILD_TESTING)
#    add_subdirectory("_test")
#endif()
add_subdirectory("cmake")

# =============================================================================
# Install commands
# =============================================================================
#include(PACE_CPackConfig)

if(WIN32)
    # Don't package committed mex files
    set(EXTERNAL_IGNORE_PATTERN "*/external/glnxa64")
endif()


set(CXX_SOURCE_DIR "${CMAKE_CURRENT_LIST_DIR}")
include_directories("${CXX_SOURCE_DIR}")

set(MEX_FUNCTIONS
    "sort_pixels_by_bins"
)
if(OPENMP_FOUND AND UNIX AND Matlab_RELEASE STREQUAL "R2018b")
    set(OMP_TYPES DISABLE_DYNAMIC_SCHEDULER)
endif()

foreach(_mex_func ${MEX_FUNCTIONS})
    add_subdirectory("${_mex_func}")
endforeach(_mex_func)

add_subdirectory(sort_pixels_by_bins.tests )
