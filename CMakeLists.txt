###############################################################################
#  (c) 2024 Copyright, Real-Time Innovations, Inc. (RTI) All rights reserved. #
#                                                                             #
#  RTI grants Licensee a license to use, modify, compile, and create          #
#  derivative works of the software solely for use with RTI Connext DDS.      #
#  Licensee may redistribute copies of the software provided that all such    #
#  copies are subject to this license.                                        #
#  The software is provided "as is", with no warranty of any type, including  #
#  any warranty for fitness for any purpose. RTI is under no obligation to    #
#  maintain or support the software.  RTI shall not be liable for any         #
#  incidental or consequential damages arising out of the use or inability to #
#  use the software.                                                          #
#                                                                             #
###############################################################################

# Load the dependencies of dds-datamodel-utils and rticonnextdds-cmake-utils.
# The dds-datamodel-utils repo may be provided by setting the variable
# DDS_DATAMODELS_UTILS_DIR to point to a local copy of this repository.
# If this is not provided, this macro downloads it as a dependency.
# This macro alsso loads all necessary files from the dependencies.
# This sets two variables that may be used in the parent scope:
#   * datamodels_utils_dir: the path to the datamodels-utils repo
#   * rti_cmake_utils_dir: the path to the rticonnextdds-cmake-utils repo
macro (configure_datamodels_dependencies)

    # Get the absolute path for the dds-datamodels-utils repo if it is defined
    set(datamodels_utils_dir)
    if (DEFINED DDS_DATAMODELS_UTILS_DIR)
        get_filename_component(
            datamodels_utils_dir
            "${CMAKE_CURRENT_BINARY_DIR}/${DDS_DATAMODELS_UTILS_DIR}"
            ABSOLUTE)
    endif()

    if(NOT IS_DIRECTORY ${datamodels_utils_dir})
        set(datamodels_utils_name_ dds-datamodels-utils)

        include(FetchContent)

        # Set this policy to avoid updating any submodule repositories
        cmake_policy(SET CMP0097 NEW)

        set(datamodels_utils_dir ${CMAKE_CURRENT_BINARY_DIR}/${datamodels_utils_name_})

        # Fetch the content of the rticonnextdds-cmake-utils repo
        FetchContent_Declare(${datamodels_utils_name_}
            GIT_REPOSITORY https://github.com/rticommunity/dds-datamodels-utils.git
            GIT_TAG main
            SOURCE_DIR "${datamodels_utils_dir}"
        )
        FetchContent_MakeAvailable(${datamodels_utils_name_})

        unset(datamodels_utils_name_)
    endif()
    get_filename_component(rti_cmake_utils_dir "${datamodels_utils_dir}/rticonnextdds-cmake-utils" ABSOLUTE)

    list(APPEND CMAKE_MODULE_PATH "${datamodels_utils_dir}/cmake")
    list(APPEND CMAKE_MODULE_PATH "${rti_cmake_utils_dir}/cmake/Modules")
endmacro()


cmake_minimum_required (VERSION 3.16)

set(PROJECT_NAME DdsDatamodelsAlmas)
set(library_name rtidatamodelalmas)

# Start the project
project(${PROJECT_NAME} VERSION 1.3.1)
set(CMAKE_CXX_STANDARD 11)

# Downloads and configure the dependencies to build this datamodel
configure_datamodels_dependencies()

find_package(
    RTIConnextDDS 6.1.2
    COMPONENTS core
    REQUIRED
)

include(ConnextDdsDatamodelsGenerateCode)

connextdds_datamodels_generate_code(
    INPUT_FOLDERS "datamodel/idl"
    OUTPUT_FOLDER "${CMAKE_CURRENT_BINARY_DIR}/datamodel/idl"
    LANG C++11
    IGNORE_IDL_NAMES "ALMAS_Management_DLRL"
    IDL_DEPENDENCIES_FOLDERS "${datamodels_utils_dir}/dds"
    CODEGEN_EXTRA_ARGS "-verbosity" "1"
)

add_library(${library_name}
    SHARED
        ${GENERATED_SRC_FILES}
)

target_include_directories(${library_name}
    PRIVATE
        ${CONNEXTDDS_INCLUDE_DIRS}
        "${CMAKE_CURRENT_BINARY_DIR}/datamodel/idl"
        "${CMAKE_CURRENT_BINARY_DIR}/dependencies"
)

target_link_libraries(${library_name}
    PRIVATE
        RTIConnextDDS::cpp2_api
)
