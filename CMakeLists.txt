cmake_minimum_required(VERSION 3.30)

set(NAME automake)
set(VERSION 1.16.5)
set(EXT tar.xz)
set(SHA256 f01d58cd6d9d77fbdca9eb4bbd5ead1988228fdb73d6f7a201f5f8d6b118b469)

project("cmeel-${NAME}" VERSION "${VERSION}")

find_package(Python COMPONENTS Interpreter)
find_program(AUTOCONF autoconf)
cmake_path(GIT AUTOCONF PARENT_PATH AUTOCONF_BIN)
cmake_path(GIT AUTOCONF_BIN PARENT_PATH AUTOCONF_PREFIX)
find_path(autom4te_perllibdir name1 [path1 path2 ...])
find_path(
  AUTOM4TE_PERLLIBDIR "Autom4te/Config.pm"
  PATHS ${AUTOCONF_PREFIX}
  PATH_SUFFIXES
    "lib/python3.${Python_VERSION_MINOR}/site-packages/cmeel.prefix/share/autoconf"
)

include(ExternalProject)
ExternalProject_Add(
  ${NAME}
  URL "https://ftpmirror.gnu.org/gnu/${NAME}/${NAME}-${VERSION}.${EXT}"
  URL_HASH "SHA256=${SHA256}"
  DOWNLOAD_EXTRACT_TIMESTAMP ON
  BUILD_IN_SOURCE ON
  PATCH_COMMAND "echo" "${AUTOM4TE_PERLLIBDIR}"
  PATCH_COMMAND "ls" "${AUTOM4TE_PERLLIBDIR}"
  CONFIGURE_COMMAND
    "${CMAKE_COMMAND}" "-E" "env" "autom4te_perllibdir=${AUTOM4TE_PERLLIBDIR}"
    "./configure" "--prefix=${CMAKE_INSTALL_PREFIX}"
  BUILD_COMMAND "make"
  # TEST_COMMAND "make" "check"
  INSTALL_COMMAND "make" "install")

# dummy file for install target
install(FILES "README.md" DESTINATION "share/cmeel-${NAME}/ ")
