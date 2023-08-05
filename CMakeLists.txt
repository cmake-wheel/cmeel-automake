cmake_minimum_required(VERSION 3.20)

set(AUTOMAKE_VERSION 1.16.5)

project(cmeel-automake VERSION ${AUTOMAKE_VERSION})

include(ExternalProject)
ExternalProject_Add(
  automake
  URL "https://ftpmirror.gnu.org/gnu/automake/automake-${AUTOMAKE_VERSION}.tar.xz"
      DOWNLOAD_EXTRACT_TIMESTAMP
      ON
  BUILD_IN_SOURCE ON
  CONFIGURE_COMMAND "./configure" "--prefix=${CMAKE_INSTALL_PREFIX}"
  BUILD_COMMAND "make"
  # TEST_COMMAND "make" "check"
  INSTALL_COMMAND "make" "install")

# dummy file for install target
install(FILES README.md DESTINATION share/cmeel-automake/)
