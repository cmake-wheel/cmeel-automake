cmake_minimum_required(VERSION 3.30)

set(NAME automake)
set(VERSION 1.16.5)
set(EXT tar.xz)
set(SHA1 32fb36e73568271ff506b60c55a6170b67681375)

project("cmeel-${NAME}" VERSION "${VERSION}")

find_package(Python REQUIRED COMPONENTS Interpreter)

set(_sp "lib/python3.${Python_VERSION_MINOR}/site-packages")
set(_cmeel_prefix "$ENV{CMAKE_PREFIX_PATH}/${_sp}/cmeel.prefix")

file(READ "${_cmeel_prefix}/bin/autoconf" toto LIMIT 30)
message(WARNING "toto: ${toto}")

find_program(
  AUTOCONF_EXECUTABLE
  NAMES autoconf
  HINTS "${_cmeel_prefix}/bin" REQUIRED)

include(ExternalProject)
ExternalProject_Add(
  ${NAME}
  URL "https://ftpmirror.gnu.org/gnu/${NAME}/${NAME}-${VERSION}.${EXT}"
  URL_HASH "SHA1=${SHA1}"
  DOWNLOAD_EXTRACT_TIMESTAMP ON
  BUILD_IN_SOURCE ON
  CONFIGURE_COMMAND
    ${CMAKE_COMMAND} -E env
    "PERL5LIB=${_cmeel_prefix}/share/autoconf:$ENV{PERL5LIB}"
    "PATH=${_cmeel_prefix}/bin:$ENV{PATH}" "autoconf" "--version"
  BUILD_COMMAND
    ${CMAKE_COMMAND} -E env
    "PERL5LIB=${_cmeel_prefix}/share/autoconf:$ENV{PERL5LIB}"
    "PATH=${_cmeel_prefix}/bin:$ENV{PATH}" "./configure"
    "--prefix=${CMAKE_INSTALL_PREFIX}"
  TEST_COMMAND "make"
  # TEST_COMMAND "make" "check"
  INSTALL_COMMAND "make" "install")

# dummy file for install target
install(FILES "README.md" DESTINATION "share/cmeel-${NAME}/")
