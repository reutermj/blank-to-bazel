# Conversion Plan Guidelines - Meson

## Overview
Guidelines for converting Meson build systems to Bazel, preserving build semantics and public interface characteristics.

## Guidelines

### Project Declaration to Bazel Module
- **Name**: Project Configuration Declaration to Bazel Module
- **Description**: Converts Meson project() function to Bazel module definition in MODULE.bazel file. Meson configuration options are converted to Bazel cc_library copts.
- **Source Build System Example**:
  ```meson
  project('project_name', 'language',
      version : 'x.y.z',
      license : 'license_type',
      meson_version : '>=x.y.z',
      default_options : [
          'c_std=standard', 'optimization=level',
          'warning_level=level'
      ]
  )
  ```
- **Bazel Example**:
  ```python
  # MODULE.bazel file
  module(
      name = "project_name",
      version = "x.y.z",
  )
  
  # BUILD file
  cc_library(
      name = "library_name",
      srcs = ["source_file.c"],
      hdrs = ["include/header_file.h"],
      includes = ["include"],
      copts = ["-std=standard", "-Olevel", "-Wall", "-Wextra"],
      visibility = ["//visibility:public"],
  )
  ```
- **Applicability**: When Meson projects have project() function calls with configuration options
- **Projects Applied**: parson

### Library Target and Dependency Declaration to Bazel cc_library
- **Name**: Library Target and Dependency Declaration to Bazel cc_library
- **Description**: Converts Meson library() and declare_dependency() functions to Bazel cc_library targets
- **Source Build System Example**:
  ```meson
  library_target = library(
      meson.project_name(),
      sources: source_files,
      install: true
  )
  
  library_dep = declare_dependency(
      include_directories : include_dirs,
      link_with : library_target
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
- **Applicability**: When Meson uses library() and declare_dependency() functions
- **Projects Applied**: parson

### Include Directory Configuration to Bazel includes
- **Name**: Include Directory Configuration to Bazel includes
- **Description**: Converts Meson include_directories() to Bazel includes attribute
- **Source Build System Example**:
  ```meson
  include_dirs = include_directories('include')
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
- **Applicability**: When Meson uses include_directories() to define header paths
- **Projects Applied**: parson

### Header Installation to Bazel hdrs
- **Name**: Header File Installation to Bazel hdrs Attribute
- **Description**: Converts Meson install_headers() to Bazel hdrs attribute for public headers
- **Source Build System Example**:
  ```meson
  install_headers('include/header_file.h')
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
- **Applicability**: When Meson uses install_headers() to make headers available to external projects
- **Projects Applied**: parson

### Root Directory Headers to Include Directory
- **Name**: Root Directory Public Headers to Include Directory Organization
- **Description**: Bazel cannot use the root directory (.) in the includes attribute. When Meson projects have public headers in the root directory that need to be accessible via includes, the conversion plan should include moving the header from the root directory into an include/ folder and updating the Bazel cc_library to reference the new location.
- **Source Build System Example**: Not applicable - this is a structural conversion requirement
- **Bazel Example**: Not applicable - this is a structural conversion requirement
- **Applicability**: When Meson projects have public headers in the root directory that need to be accessible via includes
- **Projects Applied**: parson

### Pkg-config Generation to Bazel Visibility
- **Name**: Pkg-config File Generation to Bazel Visibility
- **Description**: Converts Meson pkg-config generation to Bazel visibility rules for library discovery
- **Source Build System Example**:
  ```meson
  pkgconfig = import('pkgconfig')
  pkgconfig.generate(library_target,
      version: meson.project_version(),
      filebase: meson.project_name(),
      name: meson.project_name(),
      description: 'Library description.',
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
- **Applicability**: When Meson uses pkgconfig module to generate pkg-config files for library discovery
- **Projects Applied**: parson

### Source File Definition to Bazel srcs
- **Name**: Source File Definition to Bazel srcs Attribute
- **Description**: Converts Meson source file variable definitions to Bazel srcs attribute
- **Source Build System Example**:
  ```meson
  source_files = ['source_file.c']
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
- **Applicability**: When Meson defines source file variables for library creation
- **Projects Applied**: parson
