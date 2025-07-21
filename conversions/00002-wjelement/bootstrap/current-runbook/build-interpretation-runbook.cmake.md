# Build Interpretation Runbook Proposal - CMake

This document proposes interpretation patterns for understanding and analyzing CMake build systems based on analysis of the Parson JSON library project.

## Basic Project Structure Patterns

### [IP-CMAKE-STRUCTURE-001] CMake Version Requirement Pattern
- **Pattern Description**: Establishes minimum CMake version compatibility requirements for the build system. This pattern indicates the baseline CMake functionality required to process the build script.

- **Pattern Identification**: 
  - `cmake_minimum_required(VERSION <version>)` call
  - Typically appears at the beginning of CMakeLists.txt files
  - Version specification using semantic versioning

- **Source Example**:
```cmake
cmake_minimum_required(VERSION 3.5)
```

- **Exceptional Cases**: None encountered yet.

### [IP-CMAKE-STRUCTURE-002] Project Declaration Pattern
- **Pattern Description**: Establishes fundamental project metadata including name and supported languages. This pattern defines the project's identity and language configuration for the build system.

- **Pattern Identification**: 
  - `project(<name> <languages>)` call
  - Project name as first parameter
  - Language specification as additional parameters
  - Optional project-level configuration in project() call

- **Source Example**:
```cmake
project(myproject C)
```

- **Exceptional Cases**: None encountered yet.

### [IP-CMAKE-STRUCTURE-003] Standard Module Inclusion Pattern
- **Pattern Description**: Incorporates CMake standard modules to extend build system capabilities with predefined functionality. This pattern indicates reliance on CMake's standard library for common build tasks.

- **Pattern Identification**:
  - `include(<module_name>)` calls for standard CMake modules
  - Common modules: GNUInstallDirs, FindPackageHandleStandardArgs, etc.

- **Source Example**:
```cmake
include (GNUInstallDirs)
```

- **Exceptional Cases**: None encountered yet.

### [IP-CMAKE-STRUCTURE-004] Centralized Version Management Pattern
- **Pattern Description**: Establishes project version information in variables for reuse across multiple build components. This pattern ensures version consistency and simplifies version maintenance.

- **Pattern Identification**:
  - `set(<PROJECT>_VERSION <version>)` variable definition
  - Version variable referenced in target properties or other contexts

- **Source Example**:
```cmake
set(PROJECT_VERSION 1.2.3)
```

- **Exceptional Cases**: None encountered yet.

## Target Definition Patterns

### [IP-CMAKE-TARGET-001] Basic Library Target Declaration Pattern
- **Pattern Description**: Declares a library target with specified source files. This pattern establishes the fundamental library build target that can be configured with additional properties and requirements.

- **Pattern Identification**:
  - `add_library(<target> <sources>)` call
  - Target name as first parameter
  - Source file list as subsequent parameters

- **Source Example**:
```cmake
add_library(mylib source.c)
```

- **Exceptional Cases**: None encountered yet.

### [IP-CMAKE-TARGET-002] Public Include Directory Configuration Pattern
- **Pattern Description**: Configures public include directories for a target with separate build-time and install-time paths using generator expressions. This pattern enables proper header discovery for downstream projects in both development and installed contexts.

- **Pattern Identification**:
  - `target_include_directories()` with PUBLIC scope
  - Generator expression usage for different contexts
  - `$<INSTALL_INTERFACE:...>` for install-time include paths

- **Source Example**:
```cmake
target_include_directories(mylib PUBLIC $<INSTALL_INTERFACE:include>)
```

- **Exceptional Cases**: None encountered yet.

### [IP-CMAKE-TARGET-003] Public Header Designation Pattern
- **Pattern Description**: Designates header files as public headers for a library target. This pattern identifies which headers should be installed and made available to downstream projects consuming the library.

- **Pattern Identification**:
  - `set_target_properties()` call with PUBLIC_HEADER property
  - Header file specification as property value
  - Enables automatic header installation with target installation

- **Source Example**:
```cmake
set_target_properties(mylib PROPERTIES PUBLIC_HEADER "mylib.h")
```

- **Exceptional Cases**: None encountered yet.

### [IP-CMAKE-TARGET-004] Library Version Configuration Pattern
- **Pattern Description**: Sets the full version information for a library target. This pattern establishes the complete version string used for library identification and compatibility tracking.

- **Pattern Identification**:
  - `set_target_properties()` call with VERSION property
  - Version specification, often using project version variables
  - Full semantic version format (major.minor.patch)

- **Source Example**:
```cmake
set_target_properties(mylib PROPERTIES VERSION ${PROJECT_VERSION})
```

- **Exceptional Cases**: None encountered yet.

### [IP-CMAKE-TARGET-005] Shared Library ABI Version Pattern
- **Pattern Description**: Sets the ABI (Application Binary Interface) version for shared libraries. This pattern establishes binary compatibility versioning for shared library consumers.

- **Pattern Identification**:
  - `set_target_properties()` call with SOVERSION property
  - Version specification for shared object compatibility
  - Often matches major version or full version

- **Source Example**:
```cmake
set_target_properties(mylib PROPERTIES SOVERSION ${PROJECT_VERSION})
```

- **Exceptional Cases**: None encountered yet.

## Installation Patterns

### [IP-CMAKE-INSTALL-001] Comprehensive Installation Pattern
- **Pattern Description**: Defines complete installation rules covering all library components (runtime, libraries, archives, headers) with proper destination configuration and export setup.

- **Pattern Identification**:
  - `install(TARGETS ...)` with EXPORT specified
  - Multiple DESTINATION clauses for different component types
  - Usage of CMAKE_INSTALL_* variables for destinations
  - COMPONENT specifications for package organization

- **Source Example**:
```cmake
install(
    TARGETS mylib
    EXPORT mylibTargets
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR} COMPONENT shlib
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)
```

- **Exceptional Cases**: None encountered yet.

### [IP-CMAKE-INSTALL-002] Package Export Configuration Pattern
- **Pattern Description**: Creates CMake package configuration files that enable downstream projects to find and use the library through find_package(). This pattern implements modern CMake consumption patterns.

- **Pattern Identification**:
  - `install(EXPORT ...)` command
  - FILE parameter specifying config file name
  - NAMESPACE parameter for target prefixing
  - DESTINATION targeting cmake package directory

- **Source Example**:
```cmake
install(
    EXPORT mylibTargets
    FILE mylibConfig.cmake
    NAMESPACE mylib::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}
)
```

- **Exceptional Cases**: None encountered yet.
