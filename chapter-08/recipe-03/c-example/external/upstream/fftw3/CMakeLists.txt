find_package(FFTW3 CONFIG QUIET)
if(FFTW3_FOUND)
  get_property(_loc TARGET FFTW3::fftw3 PROPERTY LOCATION)
  message(STATUS "Found FFTW3: ${_loc} (found version ${FFTW3_VERSION})")
  add_library(fftw3_external INTERFACE)  # dummy
else()
  message(STATUS "Suitable FFTW3 could not be located. Downloading and building!")
  include(ExternalProject)
  ExternalProject_Add(fftw3_external
    URL
      http://www.fftw.org/fftw-3.3.7.tar.gz
    URL_HASH
      MD5=0d5915d7d39b3253c1cc05030d79ac47
    DOWNLOAD_NO_PROGRESS
      1
    UPDATE_COMMAND
      ""
    CMAKE_ARGS
      -DCMAKE_INSTALL_PREFIX=${DEPS_INSTALL_PREFIX}
    )
  include(GNUInstallDirs)
  set(FFTW3_DIR ${DEPS_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR}/cmake/fftw3
    CACHE PATH "Path to internally built FFTW3Config.cmake" FORCE)
endif()
