# Conversion Plan Guidelines - CMake

## Overview
Guidelines for converting CMake build systems to Bazel, preserving build semantics and public interface characteristics.

## Guidelines

### Basic Project Setup to Bazel Module
- **Name**: Basic CMake Project Setup to Bazel Module Declaration
- **Description**: Converts CMake project declaration to Bazel module definition in MODULE.bazel file. CMake minimum version has no Bazel equivalent.
- **Source Build System Example**:
  ```cmake
  cmake_minimum_required(VERSION x.y)  # No Bazel equivalent
  project(project_name VERSION x.y.z LANGUAGES C)
  ```
- **Bazel Example**:
  ```python
  # MODULE.bazel file
  module(
      name = "project_name",
      version = "x.y.z",
  )
  ```
- **Applicability**: When CMake projects have project() declarations with name and version
- **Projects Applied**: parson

### Library Target to Bazel cc_library
- **Name**: Library Target Definition to Bazel cc_library
- **Description**: Converts CMake add_library() calls with target properties to Bazel cc_library targets
- **Source Build System Example**:
  ```cmake
  add_library(library_name source_file.c)
  set_target_properties(library_name PROPERTIES PUBLIC_HEADER "include/header_file.h")
  set_target_properties(library_name PROPERTIES VERSION ${PROJECT_VERSION})
  ```
- **Bazel Example**:
  ```python
  cc_library(
      name = "library_name",
      srcs = ["source_file.c"],
      hdrs = ["include/header_file.h"],
      includes = ["include"],
      visibility = ["//visibility:public"],
  )
  ```
- **Applicability**: When CMake uses add_library() with target property configurations
- **Projects Applied**: parson

### Public Include Directories to Bazel hdrs
- **Name**: Public Include Interface Specification to Bazel hdrs and includes
- **Description**: Converts CMake target_include_directories() with PUBLIC scope to Bazel hdrs and includes attributes
- **Source Build System Example**:
  ```cmake
  target_include_directories(library_name PUBLIC $<INSTALL_INTERFACE:include>)
  ```
- **Bazel Example**:
  ```python
  cc_library(
      name = "library_name",
      srcs = ["source_file.c"],
      hdrs = ["include/header_file.h"],
      includes = ["include"],
      visibility = ["//visibility:public"],
  )
  ```
- **Applicability**: When CMake uses target_include_directories() with PUBLIC or INTERFACE scope
- **Projects Applied**: parson

### Public Header Declaration to Bazel hdrs
- **Name**: Public Header Declaration to Bazel hdrs Attribute
- **Description**: Converts CMake PUBLIC_HEADER property to Bazel hdrs attribute
- **Source Build System Example**:
  ```cmake
  set_target_properties(library_name PROPERTIES PUBLIC_HEADER "include/header_file.h")
  ```
- **Bazel Example**:
  ```python
  cc_library(
      name = "library_name",
      srcs = ["source_file.c"],
      hdrs = ["include/header_file.h"],
      includes = ["include"],
      visibility = ["//visibility:public"],
  )
  ```
- **Applicability**: When CMake uses set_target_properties() with PUBLIC_HEADER property
- **Projects Applied**: parson

### Root Directory Headers to Include Directory
- **Name**: Root Directory Public Headers to Include Directory Organization
- **Description**: Bazel cannot use the root directory (.) in the includes attribute. When CMake projects have public headers in the root directory that need to be accessible via includes, the conversion plan should include moving the header from the root directory into an include/ folder and updating the Bazel cc_library to reference the new location.
- **Source Build System Example**: Not applicable - this is a structural conversion requirement
- **Bazel Example**: Not applicable - this is a structural conversion requirement
- **Applicability**: When CMake projects have public headers in the root directory that need to be accessible via includes
- **Projects Applied**: parson

### Install Rules to Bazel Visibility
- **Name**: Install Rules and Export Configuration to Bazel Visibility
- **Description**: Converts CMake install() commands with EXPORT to Bazel visibility rules. CMake install() with EXPORT makes targets publicly available for external consumption, which corresponds to Bazel targets with public visibility.
- **Source Build System Example**:
  ```cmake
  install(
      TARGETS library_name
      EXPORT libraryTargets
      LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
      PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
  )
  install(
      EXPORT libraryTargets
      FILE libraryConfig.cmake
      NAMESPACE library::
      DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}
  )
  ```
- **Bazel Example**:
  ```python
  cc_library(
      name = "library_name",
      srcs = ["source_file.c"],
      hdrs = ["include/header_file.h"],
      includes = ["include"],
      visibility = ["//visibility:public"],
  )
  ```
- **Applicability**: When CMake uses install() with TARGETS and EXPORT for external consumption
- **Projects Applied**: parson

### Version Management to Bazel Module Version
- **Name**: Project Version Handling to Bazel Module Version
- **Description**: Converts CMake version variables and target properties to Bazel module version in MODULE.bazel file. CMake VERSION and SOVERSION properties don't translate to Bazel targets since Bazel prefers static linking.
- **Source Build System Example**:
  ```cmake
  set(PROJECT_VERSION x.y.z)
  set_target_properties(library_name PROPERTIES VERSION ${PROJECT_VERSION})
  set_target_properties(library_name PROPERTIES SOVERSION ${PROJECT_VERSION})
  ```
- **Bazel Example**:
  ```python
  # MODULE.bazel file
  module(
      name = "project_name",
      version = "x.y.z",
  )
  ```
- **Applicability**: When CMake sets version variables and applies them to target properties
- **Projects Applied**: parson

### Module Import to Bazel Rules
- **Name**: Module Import to Bazel Rules and Macros
- **Description**: Converts CMake module imports like GNUInstallDirs to equivalent Bazel rules or built-in functionality
- **Source Build System Example**:
  ```cmake
  include(StandardModule)
  ```
- **Bazel Example**:
  ```python
  # No direct equivalent - Bazel handles installation differently
  # Standard directories are handled by Bazel's built-in mechanisms
  ```
- **Applicability**: When CMake uses include() to import standard modules
- **Projects Applied**: parson
