cmake_minimum_required(VERSION 3.5)

project(netlibnet LANGUAGES C)

# Enable ExternalProject CMake module
include(ExternalProject)

# Set default ExternalProject root directory
set_directory_properties(PROPERTIES EP_PREFIX ${CMAKE_BINARY_DIR}/ThirdParty)

# Add libnet
ExternalProject_Add(
    libnet
    URL https://nchc.dl.sourceforge.net/project/libnet-dev/libnet-1.1.6.tar.gz
    TIMEOUT 30
    CONFIGURE_COMMAND <SOURCE_DIR>/configure --prefix=<INSTALL_DIR>
    BUILD_IN_SOURCE ON
    # Wrap download, configure, build and install steps in a script to log output
    LOG_DOWNLOAD ON
    LOG_CONFIGURE ON
    LOG_BUILD ON
    LOG_INSTALL ON)

# Specify include dir
ExternalProject_Get_Property(libnet install_DIR)
find_library(LIBNET_LIBRARIES
    NAMES net
    HINT ${install_DIR}/lib
)



add_executable(netlibnet main.c libnet_test.h)
add_dependencies(netlibnet libnet)
target_include_directories(netlibnet PRIVATE ${install_DIR}/include)
target_link_libraries(netlibnet PRIVATE
    ${LIBNET_LIBRARIES}
)
