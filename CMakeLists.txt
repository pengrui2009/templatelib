cmake_minimum_required(VERSION 3.0)

set(project tlib)
set(version 1.0.0)

project(${project} VERSION 1.2.11 LANGUAGES CXX)

add_library(${project} SHARED src/apple.cpp)

# namespaced alias
# add_library(${project}::${component} ALIAS ${component})

target_include_directories(${project}
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
)

include(CMakePackageConfigHelpers)
include(CMakeDependentOption)
include(CheckCXXCompilerFlag)
include(GNUInstallDirs)

set_target_properties(${project} PROPERTIES
  VERSION "${PROJECT_VERSION}"
  SOVERSION "${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}"
  PROJECT_LABEL "${project} ${${project}-label-postfix}"
  DEBUG_POSTFIX "${CMAKE_DEBUG_POSTFIX}")

# FIXME(felix2012): A more common place for the cmake export would be
# `CMAKE_INSTALL_LIBDIR`, as e.g. done in ubuntu or in this project for GTest
set(CONFIG_EXPORT_DIR "${CMAKE_INSTALL_DATADIR}/cmake/${project}")
set(EXPORT_TARGETS ${project})

configure_package_config_file(
  "${PROJECT_SOURCE_DIR}/cmake/${project}-config.cmake.in"
  "${PROJECT_BINARY_DIR}/${project}-config.cmake"
  INSTALL_DESTINATION "${CONFIG_EXPORT_DIR}"
  PATH_VARS CMAKE_INSTALL_INCLUDEDIR CONFIG_EXPORT_DIR)
unset(EXPORT_TARGETS)

write_basic_package_version_file(
  "${PROJECT_BINARY_DIR}/${project}-config-version.cmake"
  COMPATIBILITY AnyNewerVersion)

# configure_file(${project}.pc.in ${project}.pc @ONLY)

install(TARGETS ${project}
  EXPORT ${project}-targets
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR})
install(DIRECTORY ${PROJECT_SOURCE_DIR}/include/
  DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
              FILES_MATCHING PATTERN "*.h")
install(EXPORT ${project}-targets
  DESTINATION "${CONFIG_EXPORT_DIR}")
install(FILES
    "${PROJECT_BINARY_DIR}/${project}-config.cmake"
    "${PROJECT_BINARY_DIR}/${project}-config-version.cmake"
  DESTINATION "${CONFIG_EXPORT_DIR}")
# install(FILES "${PROJECT_BINARY_DIR}/${project}.pc"
  # DESTINATION ${CMAKE_INSTALL_DATADIR}/pkgconfig)
