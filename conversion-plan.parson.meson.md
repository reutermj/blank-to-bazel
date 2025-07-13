# Conversion Plan - Parson - Meson

## Project Level
- **Source Build System**: Meson
- **Target Build System**: Bazel
- **Build Assessment Reference**: `build-assessment.parson.meson.md`

## Conversion Items

### 1. Project Declaration
- **Assessment Reference**: Build Step 1 - Project Declaration (project('parson', 'c', version: '1.5.3', default_options: ['c_std=c89', 'optimization=2', 'warning_level=2']))
- **Conversion Description**: Convert Meson project declaration to Bazel package with equivalent build settings
- **Bazel Implementation**:
  ```python
  package(default_visibility = ["//visibility:public"])
  
  cc_library(
      name = "parson",
      srcs = ["parson.c"],
      hdrs = ["parson.h"],
      copts = ["-std=c89", "-O2", "-Wall", "-Wextra"],
      defines = ["PARSON_VERSION=\\\"1.5.3\\\""],
      visibility = ["//visibility:public"],
  )
  ```
- **Conversion Rationale**: Bazel package provides project-level configuration; cc_library copts provide equivalent build options
- **Guideline Reference**: Project Declaration to Bazel Package

### 2. Source File Definition
- **Assessment Reference**: Build Step 2 - Source File Definition (parson_sources = ['parson.c'])
- **Conversion Description**: Convert Meson source file variable to Bazel cc_library srcs attribute
- **Bazel Implementation**:
  ```python
  cc_library(
      name = "parson",
      srcs = ["parson.c"],
      hdrs = ["parson.h"],
      visibility = ["//visibility:public"],
  )
  ```
- **Conversion Rationale**: Bazel srcs attribute provides equivalent functionality to Meson source file variables
- **Guideline Reference**: Source File Definition to Bazel srcs

### 3. Include Directory Configuration
- **Assessment Reference**: Build Step 3 - Include Directory Configuration (parson_inc = include_directories('.'))
- **Conversion Description**: Convert Meson include_directories to Bazel includes attribute
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
- **Conversion Rationale**: Bazel includes attribute provides equivalent functionality to Meson include_directories
- **Guideline Reference**: Include Directory Configuration to Bazel includes

### 4. Library Target Creation
- **Assessment Reference**: Build Step 4 - Library Target Creation (parson_lib = library(meson.project_name(), sources: parson_sources, install: true))
- **Conversion Description**: Convert Meson library function to Bazel cc_library
- **Bazel Implementation**:
  ```python
  cc_library(
      name = "parson",
      srcs = ["parson.c"],
      hdrs = ["parson.h"],
      visibility = ["//visibility:public"],
  )
  ```
- **Conversion Rationale**: Bazel cc_library provides equivalent functionality to Meson library with install enabled
- **Guideline Reference**: Library Target and Dependency Declaration to Bazel cc_library

### 5. Header Installation
- **Assessment Reference**: Build Step 5 - Header Installation (install_headers('parson.h'))
- **Conversion Description**: Convert Meson install_headers to Bazel hdrs attribute
- **Bazel Implementation**:
  ```python
  cc_library(
      name = "parson",
      srcs = ["parson.c"],
      hdrs = ["parson.h"],
      visibility = ["//visibility:public"],
  )
  ```
- **Conversion Rationale**: Bazel hdrs attribute provides equivalent functionality to Meson install_headers for public headers
- **Guideline Reference**: Header Installation to Bazel hdrs

### 6. Dependency Declaration
- **Assessment Reference**: Build Step 6 - Dependency Declaration (parson = declare_dependency(include_directories: parson_inc, link_with: parson_lib))
- **Conversion Description**: Convert Meson declare_dependency to Bazel cc_library with public visibility
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
- **Conversion Rationale**: Bazel cc_library with public visibility provides equivalent functionality to Meson declare_dependency for external consumption
- **Guideline Reference**: Library Target and Dependency Declaration to Bazel cc_library

### 7. Pkgconfig Module Import
- **Assessment Reference**: Build Step 7 - Pkgconfig Module Import (pkgconfig = import('pkgconfig'))
- **Conversion Description**: No explicit conversion needed - Bazel handles library discovery differently
- **Bazel Implementation**:
  ```python
  # No BUILD file equivalent needed - Bazel handles library discovery through built-in mechanisms
  ```
- **Conversion Rationale**: Bazel doesn't require explicit module imports for library discovery
- **Guideline Reference**: Pkg-config Generation to Bazel Visibility

### 8. Pkg-config File Generation
- **Assessment Reference**: Build Step 8 - Pkg-config File Generation (pkgconfig.generate(parson_lib, version: meson.project_version()))
- **Conversion Description**: Convert Meson pkg-config generation to Bazel visibility for library discovery
- **Bazel Implementation**:
  ```python
  cc_library(
      name = "parson",
      srcs = ["parson.c"],
      hdrs = ["parson.h"],
      visibility = ["//visibility:public"],
  )
  ```
- **Conversion Rationale**: Bazel visibility rules provide equivalent functionality to pkg-config for library discovery
- **Guideline Reference**: Pkg-config Generation to Bazel Visibility

### 9. Public Interface - parson.h
- **Assessment Reference**: Public Interface Analysis - Public Headers (parson.h explicitly installed via install_headers())
- **Conversion Description**: Convert Meson public header installation to Bazel hdrs attribute
- **Bazel Implementation**:
  ```python
  cc_library(
      name = "parson",
      srcs = ["parson.c"],
      hdrs = ["parson.h"],
      visibility = ["//visibility:public"],
  )
  ```
- **Conversion Rationale**: Bazel hdrs attribute explicitly declares public headers equivalent to Meson install_headers
- **Guideline Reference**: Header Installation to Bazel hdrs

### 10. Public Interface - parson_lib
- **Assessment Reference**: Public Interface Analysis - Public Libraries (parson_lib intended for external use with install: true)
- **Conversion Description**: Convert Meson public library to Bazel cc_library with public visibility
- **Bazel Implementation**:
  ```python
  cc_library(
      name = "parson",
      srcs = ["parson.c"],
      hdrs = ["parson.h"],
      visibility = ["//visibility:public"],
  )
  ```
- **Conversion Rationale**: Bazel cc_library with public visibility provides equivalent functionality to Meson library with install enabled
- **Guideline Reference**: Library Target and Dependency Declaration to Bazel cc_library

### 11. Public Interface - parson dependency
- **Assessment Reference**: Public Interface Analysis - Public Libraries (parson dependency object created for external projects)
- **Conversion Description**: Convert Meson dependency object to Bazel cc_library with public visibility
- **Bazel Implementation**:
  ```python
  cc_library(
      name = "parson",
      srcs = ["parson.c"],
      hdrs = ["parson.h"],
      visibility = ["//visibility:public"],
  )
  ```
- **Conversion Rationale**: Bazel cc_library with public visibility provides equivalent functionality to Meson declare_dependency
- **Guideline Reference**: Library Target and Dependency Declaration to Bazel cc_library

## Complete Bazel BUILD File

```python
package(default_visibility = ["//visibility:public"])

cc_library(
    name = "parson",
    srcs = ["parson.c"],
    hdrs = ["parson.h"],
    copts = ["-std=c89", "-O2", "-Wall", "-Wextra"],
    defines = ["PARSON_VERSION=\\\"1.5.3\\\""],
    includes = ["."],
    visibility = ["//visibility:public"],
)
```

## Dependency Mapping

### Build Step Dependencies Preserved
- **Source File Dependencies**: Meson's parson_sources variable preserved in Bazel cc_library srcs
- **Include Directory Dependencies**: Meson's parson_inc preserved in Bazel includes attribute
- **Library Dependencies**: Meson's library target preserved in Bazel cc_library
- **Dependency Object Dependencies**: Meson's declare_dependency preserved in Bazel visibility

### Public/Private Interface Preservation
- **Public Interface**: parson.h explicitly marked as public through hdrs attribute and public visibility
- **Public Library**: parson_lib marked as public through visibility configuration
- **External Consumption**: Meson's install and declare_dependency mechanism preserved through Bazel visibility rules
- **Pkg-config Support**: Meson's pkg-config generation replaced by Bazel's native dependency system

### Build Configuration Mapping
- **Project Options**: Meson's default_options (c_std=c89, optimization=2, warning_level=2) mapped to Bazel copts
- **Version Configuration**: Meson's project version mapped to Bazel defines
- **Installation Configuration**: Meson's install settings mapped to Bazel visibility rules
- **Library Discovery**: Meson's pkg-config and declare_dependency replaced by Bazel's target referencing system
