# Build Assessment Guidelines - Meson

## Overview
Guidelines for assessing Meson build systems to ensure comprehensive understanding of project structure, dependencies, and build semantics.

## Guidelines

### Project Declaration
- **Name**: Project Configuration Declaration
- **Description**: Identifies fundamental Meson project setup including name, language, version, and default options
- **When to Apply**: When analyzing project() function calls with configuration options
- **Source Code Patterns**:
  ```meson
  project('name', 'language',
      version : 'x.y.z',
      license : 'license_type',
      meson_version : '>=x.y.z',
      default_options : [
          'option=value'
      ]
  )
  ```
- **Projects Applied**: parson

### Library Creation and Dependencies
- **Name**: Library Target and Dependency Declaration
- **Description**: Documents how Meson creates library targets and declares dependencies
- **When to Apply**: When library() and declare_dependency() functions are used
- **Source Code Patterns**:
  ```meson
  lib = library(
      'name',
      sources: source_files,
      install: true
  )
  
  dep = declare_dependency(
      include_directories : includes,
      link_with : lib
  )
  ```
- **Projects Applied**: parson

### Include Directory Management
- **Name**: Include Directory Configuration
- **Description**: Tracks how include directories are defined and used
- **When to Apply**: When include_directories() is used to define header paths
- **Source Code Patterns**:
  ```meson
  inc = include_directories('path')
  ```
- **Projects Applied**: parson

### Header Installation
- **Name**: Header File Installation
- **Description**: Documents how header files are installed for public use
- **When to Apply**: When install_headers() is used
- **Source Code Patterns**:
  ```meson
  install_headers('header.h')
  ```
- **Projects Applied**: parson

### Pkg-config Generation
- **Name**: Pkg-config File Generation
- **Description**: Identifies how Meson generates pkg-config files for library discovery
- **When to Apply**: When pkgconfig module is imported and generate() is used
- **Source Code Patterns**:
  ```meson
  pkgconfig = import('pkgconfig')
  pkgconfig.generate(library_target,
      version: version,
      filebase: name,
      name: name,
      description: 'description'
  )
  ```
- **Projects Applied**: parson

### Public Interface Identification
- **Name**: Public Header Installation
- **Description**: Identifies headers that are part of the public API through install_headers()
- **When to Apply**: When install_headers() is used to make headers available to external projects
- **Source Code Patterns**:
  ```meson
  install_headers('header.h')
  ```
- **Projects Applied**: parson

### Public Library Interface
- **Name**: Public Library Target
- **Description**: Identifies library targets intended for external use through installation and dependency declaration
- **When to Apply**: When library targets are installed and dependency objects are declared for external consumption
- **Source Code Patterns**:
  ```meson
  lib = library('name', install: true)
  dep = declare_dependency(link_with: lib)
  ```
- **Projects Applied**: parson
