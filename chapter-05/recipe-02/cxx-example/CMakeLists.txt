cmake_minimum_required(VERSION 3.5 FATAL_ERROR)

project(recipe-02 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(PythonInterp REQUIRED)
find_package(PythonLibs REQUIRED)

execute_process(
  COMMAND
    ${PYTHON_EXECUTABLE} "-c" "import numpy; print(numpy.__version__)"
  OUTPUT_VARIABLE NumPy_VERSION
  OUTPUT_STRIP_TRAILING_WHITESPACE
  )

message(STATUS "NumPy version: ${NumPy_VERSION}")

configure_file(Report.hpp.in Report.hpp @ONLY)

add_executable(pure-embedding "")

if(PYTHON_VERSION_MAJOR VERSION_LESS 3)
  target_sources(pure-embedding
    PRIVATE
      Py2-pure-embedding.cpp
    )
else()
  target_sources(pure-embedding
    PRIVATE
      Py3-pure-embedding.cpp
    )
endif()

# MSYS2 on Appveyor-specific
if(WIN32 AND (NOT MSVC))
  target_compile_definitions(pure-embedding
    PUBLIC
      "_hypot=hypot"
    )
endif()

target_include_directories(pure-embedding
  PRIVATE
    ${PYTHON_INCLUDE_DIRS}
    ${CMAKE_CURRENT_BINARY_DIR}
  )

target_link_libraries(pure-embedding
  PRIVATE
    ${PYTHON_LIBRARIES}
  )

file(
  COPY ${CMAKE_CURRENT_SOURCE_DIR}/use_numpy.py
  DESTINATION ${CMAKE_CURRENT_BINARY_DIR}
  )
