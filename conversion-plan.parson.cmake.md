# Conversion Plan - Parson - CMake

## Project Level
- **Source Build System**: CMake
- **Target Build System**: Bazel
- **Build Assessment Reference**: `build-assessment.parson.cmake.md`

## Conversion Items

### 1. CMake Minimum Version Check
- **Assessment Reference**: Build Step 1 - CMake Minimum Version Check (cmake_minimum_required(VERSION 3.5))
- **Conversion Description**: No explicit conversion needed - Bazel doesn't require version declarations
- **Bazel Implementation**:
  ```python
  # No BUILD file equivalent needed - Bazel handles versioning differently
  ```
- **Conversion Rationale**: Bazel doesn't use version declarations in BUILD files; version requirements are handled by Bazel installation
- **Guideline Reference**: Basic CMake Project Setup to Bazel Package Declaration

### 2. Project Declaration
- **Assessment Reference**: Build Step 2 - Project Declaration (project(parson C))
- **Conversion Description**: Convert CMake project declaration to Bazel package declaration
- **Bazel Implementation**:
  ```python
  package(default_visibility = ["//visibility:public"])
  ```
- **Conversion Rationale**: Bazel package declaration provides project-level configuration equivalent to CMake project()
- **Guideline Reference**: Basic CMake Project Setup to Bazel Package Declaration

### 3. Module Import
- **Assessment Reference**: Build Step 3 - Module Import (include(GNUInstallDirs))
- **Conversion Description**: No explicit conversion needed - Bazel handles installation differently
- **Bazel Implementation**:
  ```python
  # No BUILD file equivalent needed - Bazel handles installation through built-in mechanisms
  ```
- **Conversion Rationale**: Bazel doesn't require explicit module imports for standard functionality
- **Guideline Reference**: Module Import to Bazel Rules

### 4. Version Configuration
- **Assessment Reference**: Build Step 4 - Version Configuration (set(PARSON_VERSION 1.5.3))
- **Conversion Description**: Convert CMake version variable to Bazel defines or version file
- **Bazel Implementation**:
  ```python
  cc_library(
      name = "parson",
      srcs = ["parson.c"],
      hdrs = ["parson.h"],
      defines = ["PARSON_VERSION=\\\"1.5.3\\\""],
      visibility = ["//visibility:public"],
  )
  ```
- **Conversion Rationale**: Bazel defines attribute provides equivalent functionality to CMake version variables
- **Guideline Reference**: Version Management to Bazel Build Settings

### 5. Library Target Creation
- **Assessment Reference**: Build Step 5 - Library Target Creation (add_library(parson parson.c))
- **Conversion Description**: Convert CMake add_library to Bazel cc_library
- **Bazel Implementation**:
  ```python
  cc_library(
      name = "parson",
      srcs = ["parson.c"],
      hdrs = ["parson.h"],
      visibility = ["//visibility:public"],
  )
  ```
- **Conversion Rationale**: Bazel cc_library provides equivalent functionality to CMake add_library
- **Guideline Reference**: Library Target to Bazel cc_library

### 6. Include Directory Configuration
- **Assessment Reference**: Build Step 6 - Include Directory Configuration (target_include_directories(parson PUBLIC $<INSTALL_INTERFACE:include>))
- **Conversion Description**: Convert CMake public include directories to Bazel includes attribute
- **Bazel Implementation**:
  ```python
  cc_library(
      name = "parson",
      srcs = ["parson.c"],
      hdrs = ["parson.h"],
      includes = ["."],
      visibility = ["//visibility:public"],
  )
  ```
- **Conversion Rationale**: Bazel includes attribute provides equivalent functionality to CMake target_include_directories PUBLIC
- **Guideline Reference**: Public Include Directories to Bazel hdrs

### 7. Public Header Configuration
- **Assessment Reference**: Build Step 7 - Public Header Configuration (set_target_properties(parson PROPERTIES PUBLIC_HEADER "parson.h"))
- **Conversion Description**: Convert CMake PUBLIC_HEADER property to Bazel hdrs attribute
- **Bazel Implementation**:
  ```python
  cc_library(
      name = "parson",
      srcs = ["parson.c"],
      hdrs = ["parson.h"],
      visibility = ["//visibility:public"],
  )
  ```
- **Conversion Rationale**: Bazel hdrs attribute explicitly declares public headers equivalent to CMake PUBLIC_HEADER
- **Guideline Reference**: Public Header Declaration to Bazel hdrs

### 8. Library Version Setting
- **Assessment Reference**: Build Step 8 - Library Version Setting (set_target_properties(parson PROPERTIES VERSION ${PARSON_VERSION}))
- **Conversion Description**: Convert CMake version properties to Bazel defines
- **Bazel Implementation**:
  ```python
  cc_library(
      name = "parson",
      srcs = ["parson.c"],
      hdrs = ["parson.h"],
      defines = ["PARSON_VERSION=\\\"1.5.3\\\""],
      visibility = ["//visibility:public"],
  )
  ```
- **Conversion Rationale**: Bazel defines provide equivalent functionality to CMake version properties for library versioning
- **Guideline Reference**: Version Management to Bazel Build Settings

### 9. Target Installation
- **Assessment Reference**: Build Step 9 - Target Installation (install(TARGETS parson EXPORT parsonTargets))
- **Conversion Description**: Convert CMake install rules to Bazel visibility configuration
- **Bazel Implementation**:
  ```python
  cc_library(
      name = "parson",
      srcs = ["parson.c"],
      hdrs = ["parson.h"],
      visibility = ["//visibility:public"],
  )
  ```
- **Conversion Rationale**: Bazel visibility rules provide equivalent functionality to CMake install/export for external consumption
- **Guideline Reference**: Install Rules to Bazel Visibility

### 10. Export Configuration Installation
- **Assessment Reference**: Build Step 10 - Export Configuration Installation (install(EXPORT parsonTargets FILE parsonConfig.cmake))
- **Conversion Description**: Convert CMake export configuration to Bazel visibility
- **Bazel Implementation**:
  ```python
  cc_library(
      name = "parson",
      srcs = ["parson.c"],
      hdrs = ["parson.h"],
      visibility = ["//visibility:public"],
  )
  ```
- **Conversion Rationale**: Bazel visibility provides equivalent functionality to CMake export configuration for external projects
- **Guideline Reference**: Install Rules to Bazel Visibility

### 11. Public Interface - parson.h
- **Assessment Reference**: Public Interface Analysis - Public Headers (parson.h explicitly marked as public header)
- **Conversion Description**: Convert CMake public header to Bazel hdrs attribute
- **Bazel Implementation**:
  ```python
  cc_library(
      name = "parson",
      srcs = ["parson.c"],
      hdrs = ["parson.h"],
      visibility = ["//visibility:public"],
  )
  ```
- **Conversion Rationale**: Bazel hdrs attribute explicitly declares public headers equivalent to CMake PUBLIC_HEADER
- **Guideline Reference**: Public Header Declaration to Bazel hdrs

### 12. Public Interface - parson library
- **Assessment Reference**: Public Interface Analysis - Public Libraries (parson library target intended for external use)
- **Conversion Description**: Convert CMake public library to Bazel cc_library with public visibility
- **Bazel Implementation**:
  ```python
  cc_library(
      name = "parson",
      srcs = ["parson.c"],
      hdrs = ["parson.h"],
      visibility = ["//visibility:public"],
  )
  ```
- **Conversion Rationale**: Bazel cc_library with public visibility provides equivalent functionality to CMake library with install/export
- **Guideline Reference**: Install Rules to Bazel Visibility

## Complete Bazel BUILD File

```python
package(default_visibility = ["//visibility:public"])

cc_library(
    name = "parson",
    srcs = ["parson.c"],
    hdrs = ["parson.h"],
    defines = ["PARSON_VERSION=\\\"1.5.3\\\""],
    includes = ["."],
    visibility = ["//visibility:public"],
)
```

## Dependency Mapping

### Build Step Dependencies Preserved
- **Library Target → Source Dependencies**: CMake's add_library(parson parson.c) preserved in Bazel cc_library srcs
- **Public Header Dependencies**: CMake's PUBLIC_HEADER property preserved in Bazel hdrs attribute
- **Public Include Dependencies**: CMake's target_include_directories PUBLIC preserved in Bazel includes attribute
- **Version Dependencies**: CMake's version configuration preserved in Bazel defines

### Public/Private Interface Preservation
- **Public Interface**: parson.h explicitly marked as public through hdrs attribute and public visibility
- **Public Library**: parson library marked as public through visibility configuration
- **External Consumption**: CMake's install/export mechanism preserved through Bazel visibility rules
- **Namespace Equivalent**: CMake's parson:: namespace functionality provided by Bazel target referencing

### Installation and Export Mapping
- **Install Rules**: CMake's install(TARGETS) mapped to Bazel visibility for external consumption
- **Export Configuration**: CMake's export configuration mapped to Bazel's built-in target referencing
- **Find Package Support**: CMake's find_package() support replaced by Bazel's native dependency system
