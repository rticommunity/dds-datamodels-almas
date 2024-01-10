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

# Download dependencies from the dds-datamodels-utils repo and load all
# necessary files from it.
# This setns two variables that may be used:
#   * datamodels_utils: the name of the datamodels-utils folder
#   * rti_cmake_utils: the name of the rticonnextdds-cmake-utils folder
macro (configure_datamodels_dependencies)

    include(FetchContent)

    # Set this policy to avoid updating any submodule repositories
    cmake_policy(SET CMP0097 NEW)

    # Fetch the content of the rticonnextdds-cmake-utils repo
    set(datamodels_utils dds-datamodels-utils)
    set(rti_cmake_utils rticonnextdds-cmake-utils)
    FetchContent_Declare(${datamodels_utils}
        GIT_REPOSITORY https://github.com/rticommunity/dds-datamodels-utils.git
        GIT_TAG main
        SOURCE_DIR "${CMAKE_CURRENT_BINARY_DIR}/${datamodels_utils}"
    )

    FetchContent_MakeAvailable(${datamodels_utils})

    list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_BINARY_DIR}/${datamodels_utils}/cmake")
    list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_BINARY_DIR}/${datamodels_utils}/${rti_cmake_utils}/cmake/Modules")

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
    OUTPUT_FOLDER "${CMAKE_CURRENT_BINARY_DIR}/datamodel/idl/"
    LANG C++11
    IGNORE_IDL_NAMES "ALMAS_Management_DLRL"
    IDL_DEPENDENCIES_FOLDERS "${CMAKE_CURRENT_BINARY_DIR}/${datamodels_utils}/dds"
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
        "${CMAKE_CURRENT_BINARY_DIR}/dependencies/"
)

target_link_libraries(${library_name}
    PRIVATE
        RTIConnextDDS::cpp2_api
)
