cmake_minimum_required(VERSION 3.7)

project(SoftEtherVPN
  VERSION 5.01.9657
  LANGUAGES C
)

set(default_build_type "Release")

# Check that submodules are present only if source was downloaded with git
if(EXISTS "${SoftEtherVPN_SOURCE_DIR}/.git" AND NOT EXISTS "${SoftEtherVPN_SOURCE_DIR}/src/Mayaqua/cpu_features/CMakeLists.txt")
    message (FATAL_ERROR "Submodules are not initialized. Run\n\tgit submodule update --init --recursive")
endif()

# Compare ${PROJECT_VERSION} and src/CurrentBuild.txt
file(READ ${SoftEtherVPN_SOURCE_DIR}/src/CurrentBuild.txt CurrentBuild)

string(REGEX MATCH "VERSION_MAJOR ([0-9]+)" temp ${CurrentBuild})
string(REGEX REPLACE "VERSION_MAJOR ([0-9]+)" "\\1" CurrentBuild_MAJOR ${temp})
string(REGEX MATCH "VERSION_MINOR ([0-9]+)" temp ${CurrentBuild})
string(REGEX REPLACE "VERSION_MINOR ([0-9]+)" "\\1" CurrentBuild_MINOR ${temp})
string(REGEX MATCH "VERSION_BUILD ([0-9]+)" temp ${CurrentBuild})
string(REGEX REPLACE "VERSION_BUILD ([0-9]+)" "\\1" CurrentBuild_BUILD ${temp})

if (NOT ${PROJECT_VERSION} VERSION_EQUAL "${CurrentBuild_MAJOR}.${CurrentBuild_MINOR}.${CurrentBuild_BUILD}")
  message (FATAL_ERROR "PROJECT_VERSION does not match to src/CurrentBuild.txt")
endif()

set(BUILD_DIRECTORY ${SoftEtherVPN_SOURCE_DIR}/build)

add_subdirectory(src)

# Packaging
set(CPACK_COMPONENTS_ALL vpnserver vpnclient vpnbridge vpncmd)
set(CPACK_PACKAGE_DIRECTORY ${BUILD_DIRECTORY})
set(CPACK_PACKAGE_VERSION ${PROJECT_VERSION})
set(CPACK_PACKAGE_VENDOR "SoftEther")
set(CPACK_PACKAGE_NAME "softether")
set(CPACK_PACKAGE_DESCRIPTION_FILE "${SoftEtherVPN_SOURCE_DIR}/description")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "SoftEther VPN is an open-source cross-platform multi-protocol VPN program, created as an academic project in the University of Tsukuba.")

# DEB
set(CPACK_DEB_COMPONENT_INSTALL ON)
set(CPACK_DEBIAN_PACKAGE_SHLIBDEPS ON)
set(CPACK_DEBIAN_FILE_NAME "DEB-DEFAULT")
set(CPACK_DEBIAN_PACKAGE_SECTION "net")
set(CPACK_DEBIAN_PACKAGE_MAINTAINER "Unknown")

# RPM
set(CPACK_RPM_COMPONENT_INSTALL ON)
set(CPACK_RPM_FILE_NAME "RPM-DEFAULT")
set(CPACK_RPM_PACKAGE_GROUP "Applications/Internet")
set(CPACK_RPM_PACKAGE_LICENSE "GPLv2")

include(CPack)
