# Build Interpretation Runbook Proposal - Meson

This document proposes interpretation patterns for understanding and analyzing Meson build systems based on analysis of the Parson JSON library project.

## Project Configuration Patterns

### [IP-MESON-PROJECT-001] - Basic Project Declaration Pattern
**Pattern Description**: Establishes fundamental project identity including name and supported languages. This pattern defines the project's basic identity and language configuration for the build system.

**Pattern Identification**:
- `project()` function call with project name as first parameter
- Language specification as positional parameters
- Minimal project declaration without extensive metadata

**Source Example**:
```python
project('myproject', 'c'
    # other parameters ommitted for brevity
)
```

**Exceptional Cases**: None encountered yet.

### [IP-MESON-PROJECT-002] - Project Version Declaration Pattern
**Pattern Description**: Establishes project version information within the project declaration. This pattern enables version-aware build system behavior and package generation.

**Pattern Identification**:
- `project()` function call with `version` parameter
- Version specification using semantic versioning format
- Version information accessible via `meson.project_version()`

**Source Example**:
```python
project('myproject', 'c',
    # other parameters ommitted for brevity
    version : '1.2.3'
)
```

**Exceptional Cases**: None encountered yet.

### [IP-MESON-PROJECT-003] - Project License Declaration Pattern
**Pattern Description**: Establishes project license information for package management and distribution purposes. This pattern provides license metadata for generated packages and build artifacts.

**Pattern Identification**:
- `project()` function call with `license` parameter
- Standard license identifier (SPDX format recommended)
- License information used in package generation

**Source Example**:
```python
project('myproject', 'c',
    # other parameters ommitted for brevity
    license : 'MIT'
)
```

**Exceptional Cases**: None encountered yet.

### [IP-MESON-PROJECT-004] - Meson Version Requirement Pattern
**Pattern Description**: Establishes minimum Meson version compatibility requirements for the build system. This pattern indicates the baseline Meson functionality required to process the build script.

**Pattern Identification**:
- `project()` function call with `meson_version` parameter
- Version constraint specification using comparison operators
- Compatibility requirement for build system features

**Source Example**:
```python
project('myproject', 'c',
    # other parameters ommitted for brevity
    meson_version : '>=0.46.0'
)
```

**Exceptional Cases**: None encountered yet.

### [IP-MESON-PROJECT-005] - Default Build Options Pattern
**Pattern Description**: Establishes project-wide default build configuration options. This pattern sets consistent build behavior across all targets within the project.

**Pattern Identification**:
- `project()` function call with `default_options` array parameter
- Build option specifications in key=value format
- Options affecting compilation, optimization, and warnings

**Source Example**:
```python
project('myproject', 'c',
    # other parameters ommitted for brevity
    default_options : [
        'c_std=c89', 'optimization=2',
        'warning_level=2'
        ]
)
```

**Exceptional Cases**: None encountered yet.

## Source Organization Patterns

### [IP-MESON-SOURCE-001] - Explicit Source and Include Configuration Pattern
**Pattern Description**: Organizes source files and include directories through dedicated variables before target creation. This pattern promotes maintainable build scripts through clear separation of configuration data.

**Pattern Identification**:
- Source files defined in dedicated array variables
- `include_directories()` function calls assigned to variables
- Variables referenced in subsequent target definitions

**Source Example**:
```python
project_sources = ['source.c']

project_inc = include_directories('.')
```

**Exceptional Cases**: None encountered yet.

## Target Definition Patterns

### [IP-MESON-TARGET-001] - Basic Library Target Pattern
**Pattern Description**: Creates a basic library target with a specified name. This pattern establishes the fundamental library build target without additional configuration.

**Pattern Identification**:
- `library()` function call with target name as first parameter
- Minimal library target declaration
- Target name assigned to variable for reference

**Source Example**:
```python
project_lib = library('mylibrary'
    # other parameters ommitted for brevity
)
```

**Exceptional Cases**: None encountered yet.

### [IP-MESON-TARGET-002] - Library Target with Sources Pattern
**Pattern Description**: Associates source files with a library target. This pattern specifies which source files should be compiled into the library.

**Pattern Identification**:
- `library()` function call with `sources` parameter
- Source specification via variable reference or direct file list
- Source files compiled into the target

**Source Example**:
```python
project_lib = library('mylibrary',
    # other parameters ommitted for brevity
    sources: project_sources
)
```

**Exceptional Cases**: None encountered yet.

### [IP-MESON-TARGET-003] - Library Target Installation Pattern
**Pattern Description**: Enables system installation for a library target. This pattern makes the library available for system-wide installation and use by other projects.

**Pattern Identification**:
- `library()` function call with `install: true` parameter
- Installation enabled for the target
- Library becomes available for system-wide use

**Source Example**:
```python
project_lib = library('mylibrary',
    # other parameters ommitted for brevity
    install: true
)
```

**Exceptional Cases**: None encountered yet.

### [IP-MESON-TARGET-004] - Project-Named Library Target Pattern
**Pattern Description**: Creates library targets using project metadata for naming consistency. This pattern ensures library naming aligns with project identity.

**Pattern Identification**:
- `library()` function call with `meson.project_name()` as target name
- Dynamic naming based on project declaration
- Consistent naming across project components

**Source Example**:
```python
project_lib = library(meson.project_name()
    # other parameters ommitted for brevity
)
```

**Exceptional Cases**: None encountered yet.

### [IP-MESON-TARGET-005] - Explicit Header Installation Pattern
**Pattern Description**: Separates header installation from library installation to provide granular control over public API exposure. This pattern enables independent management of headers and binaries.

**Pattern Identification**:
- `install_headers()` function call separate from library target
- Direct file specification rather than target property reference

**Source Example**:
```python
install_headers('myproject.h')
```

**Exceptional Cases**: None encountered yet.

## Dependency Management Patterns

### [IP-MESON-DEPENDENCY-001] - Encapsulated Dependency Declaration Pattern
**Pattern Description**: Creates dependency objects that encapsulate both include directories and library linking requirements for reuse across targets or subprojects. This pattern enables clean integration in larger build hierarchies.

**Pattern Identification**:
- `declare_dependency()` function call
- `include_directories` and `link_with` parameters
- Dependency object assigned to variable for reuse

**Source Example**:
```python
project_dep = declare_dependency(
    include_directories : project_inc,
    link_with : project_lib
)
```

**Exceptional Cases**: None encountered yet.

## Package Integration Patterns

### [IP-MESON-PACKAGE-001] - Pkg-config Generation Pattern
**Pattern Description**: Generates pkg-config files for integration with traditional Unix package management systems. This pattern bridges modern Meson builds with conventional package discovery mechanisms.

**Pattern Identification**:
- `import('pkgconfig')` module import
- `pkgconfig.generate()` function call
- Library target passed as first parameter
- Project metadata used for pkg-config file properties

**Source Example**:
```python
pkgconfig = import('pkgconfig')

# will create a pkg config
pkgconfig.generate(project_lib,
    version: meson.project_version(),
    filebase: meson.project_name(),
    name: meson.project_name(),
    description: 'A library for common functionality.',
)
```

**Exceptional Cases**: None encountered yet.
