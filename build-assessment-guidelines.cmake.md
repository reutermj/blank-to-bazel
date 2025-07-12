# Build Assessment Guidelines - CMake

## Overview
Guidelines for assessing CMake build systems to ensure comprehensive understanding of project structure, dependencies, and build semantics.

## Guidelines

### Basic Project Structure
- **Name**: Basic CMake Project Setup
- **Description**: Identifies fundamental CMake project configuration including minimum version, project name, and language
- **When to Apply**: When analyzing any CMake project with cmake_minimum_required and project declarations
- **Source Code Patterns**: 
  ```cmake
  cmake_minimum_required(VERSION x.y)
  project(project_name LANGUAGES)
  ```
- **Projects Applied**: parson

### Library Creation and Configuration
- **Name**: Library Target Definition
- **Description**: Documents how CMake creates library targets with sources and configurations
- **When to Apply**: When encountering add_library() calls with target property configurations
- **Source Code Patterns**:
  ```cmake
  add_library(target_name source_files)
  set_target_properties(target_name PROPERTIES property_name value)
  target_include_directories(target_name scope directories)
  ```
- **Projects Applied**: parson

### Installation Configuration
- **Name**: Install Rules and Export Configuration
- **Description**: Identifies how CMake handles installation of targets, headers, and export configurations
- **When to Apply**: When install() commands are present with TARGETS, EXPORT, or file installations
- **Source Code Patterns**:
  ```cmake
  install(TARGETS target_name
          EXPORT export_name
          DESTINATION destinations)
  install(EXPORT export_name
          FILE config_file
          NAMESPACE namespace
          DESTINATION path)
  ```
- **Projects Applied**: parson

### Version Management
- **Name**: Project Version Handling
- **Description**: Tracks how version information is defined and applied to targets
- **When to Apply**: When version variables are set and applied to target properties
- **Source Code Patterns**:
  ```cmake
  set(PROJECT_VERSION x.y.z)
  set_target_properties(target PROPERTIES VERSION ${PROJECT_VERSION})
  set_target_properties(target PROPERTIES SOVERSION ${PROJECT_VERSION})
  ```
- **Projects Applied**: parson

### Public Interface Identification
- **Name**: Public Header Declaration
- **Description**: Identifies headers that are part of the public API through PUBLIC_HEADER property
- **When to Apply**: When set_target_properties() is used with PUBLIC_HEADER property
- **Source Code Patterns**:
  ```cmake
  set_target_properties(target_name PROPERTIES PUBLIC_HEADER "header.h")
  ```
- **Projects Applied**: parson

### Public Library Interface
- **Name**: Public Library Target
- **Description**: Identifies library targets intended for external use through installation and export
- **When to Apply**: When library targets are installed with EXPORT configuration for external consumption
- **Source Code Patterns**:
  ```cmake
  install(TARGETS target_name EXPORT export_name)
  install(EXPORT export_name FILE config.cmake NAMESPACE namespace::)
  ```
- **Projects Applied**: parson

### Public Include Directory Declaration
- **Name**: Public Include Interface Specification
- **Description**: Identifies include directories that are part of the public interface through PUBLIC scope in target_include_directories()
- **When to Apply**: When target_include_directories() is used with PUBLIC or INTERFACE scope, indicating headers intended for external consumption
- **Source Code Patterns**:
  ```cmake
  target_include_directories(target_name PUBLIC directory_path)
  target_include_directories(target_name INTERFACE directory_path)
  target_include_directories(target_name PUBLIC $<INSTALL_INTERFACE:include>)
  ```
- **Projects Applied**: parson

### Private Include Directory Declaration
- **Name**: Private Include Interface Specification
- **Description**: Identifies include directories that are for internal use only through PRIVATE scope in target_include_directories()
- **When to Apply**: When target_include_directories() is used with PRIVATE scope, indicating headers not intended for external consumption
- **Source Code Patterns**:
  ```cmake
  target_include_directories(target_name PRIVATE directory_path)
  target_include_directories(target_name PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/internal)
  ```
- **Projects Applied**: None encountered yet
