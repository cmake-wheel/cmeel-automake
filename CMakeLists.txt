cmake_minimum_required(VERSION 3.30)

set(NAME automake)
set(VERSION 1.16.5)
set(EXT tar.xz)
set(SHA1 32fb36e73568271ff506b60c55a6170b67681375)

project("cmeel-${NAME}" VERSION "${VERSION}")

set(_cmeel_prefix "$ENV{CMAKE_PREFIX_PATH}")
set(_autoconf_share "${_cmeel_prefix}/share/autoconf")

include(ExternalProject)
ExternalProject_Add(
  ${NAME}
  URL "https://ftpmirror.gnu.org/gnu/${NAME}/${NAME}-${VERSION}.${EXT}"
  URL_HASH "SHA1=${SHA1}"
  DOWNLOAD_EXTRACT_TIMESTAMP ON
  BUILD_IN_SOURCE ON
  CONFIGURE_COMMAND
    "${CMAKE_COMMAND}" "-E" "copy" "${_autoconf_share}/autom4te.cfg"
    "${CMAKE_BINARY_DIR}/autom4te.cfg" && "sed" "-i.bak"
    "s|args: --prepend-include '[^']*'|args: --prepend-include '${_autoconf_share}'|g"
    "${CMAKE_BINARY_DIR}/autom4te.cfg" && ${CMAKE_COMMAND} -E env
    "PERL5LIB=${_autoconf_share}:$ENV{PERL5LIB}" "--" "./configure"
    "AUTOM4TE_CFG=${CMAKE_BINARY_DIR}/autom4te.cfg"
    "autom4te_perllibdir=${_autoconf_share}"
    "trailer_m4=${_autoconf_share}/autoconf/trailer.m4"
    "AUTOCONF=${_cmeel_prefix}/bin/autoconf"
    "AUTOHEADER=${_cmeel_prefix}/bin/autoheader"
    "AUTOM4TE=${_cmeel_prefix}/bin/autom4te" "M4=${_cmeel_prefix}/bin/m4"
    "--prefix=${CMAKE_INSTALL_PREFIX}"
  BUILD_COMMAND "make"
                # TEST_COMMAND "make" "check"
  INSTALL_COMMAND "make" "install")

# dummy file for install target
install(FILES "README.md" DESTINATION "share/cmeel-${NAME}/ ")
