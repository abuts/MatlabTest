set(TEST_SRC_FILES
    "sort_pixels_by_bins.test.cpp"
)

set(SRC_FILES
    "${CXX_SOURCE_DIR}/sort_pixels_by_bins/sort_pixels_by_bins.cpp"
)

set(HDR_FILES
    "${CXX_SOURCE_DIR}/sort_pixels_by_bins/sort_pixels_by_bins.h"
    "${CXX_SOURCE_DIR}/include/CommonCode.h"
)

pace_add_cpp_unit_test(
    NAME "sort_pixels_by_bins.test"
    SOURCES "${TEST_SRC_FILES}" "${SRC_FILES}" "${HDR_FILES}"
    MEX_TEST
)
#matlab_add_mex(
#    NAME "sort_pixels_by_bins.test"
#    EXECUTABLE
#    SRC "${TEST_SRC_FILES}" "${HDR_FILES}" "${SRC_FILES}"
#    LINK_TO gtest_main "${Matlab_LIBRARIES}"    
#)

set(_full_test_name "cpp.sort_pixels_by_bins.test")
# Add the test to CTest
#add_test(
#    NAME "${_full_test_name}"
#    COMMAND "${TESTS_BIN_DIR}/${_full_test_name}"
#    WORKING_DIRECTORY "${${PROJECT_NAME}_ROOT}"
#)
message(" MATLATB DLL-s: ${Matlab_DLL_DIR}")
message(" _full_test_name: ${_full_test_name}")
#if(WIN32 AND "${TEST_MEX_TEST}")
if(WIN32)
    #set_tests_properties("${_full_test_name}" PROPERTIES
    #    ENVIRONMENT "PATH=${Matlab_DLL_DIR}"
    #)
    # Set Visual Studio debugger environment variables
    # Adding the Matlab DLL directory stops errors because of missing DLLs
    # and adding ${PROJECT_NAME}_ROOT variable helps tests find data files.
#    string(TOUPPER "${PROJECT_NAME}_ROOT" _proj_root_upper)
    #set_target_properties("${_full_test_name}"
    #    PROPERTIES
    #        VS_DEBUGGER_ENVIRONMENT
    #            "PATH=${Matlab_DLL_DIR};%PATH%\n${_proj_root_upper}=${${PROJECT_NAME}_ROOT}"
    #)
endif()

