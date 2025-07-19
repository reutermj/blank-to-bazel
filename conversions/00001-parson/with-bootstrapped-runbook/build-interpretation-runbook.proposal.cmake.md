# Build Interpretation Runbook Maintenance Proposal - CMake

This document proposes improvements and clarifications to interpretation patterns for CMake build systems based on the analysis of the Parson JSON library project. The existing runbook adequately covers the build constructs encountered, with only minor clarifications needed.

## Clarified Installation Patterns

### [IP-CMAKE-INSTALL-002-REVISION] - Complete Package Export Configuration Pattern
**Pattern Description**: Creates comprehensive CMake package configuration files that enable downstream projects to use find_package() for library discovery and consumption. This pattern implements the complete modern CMake consumption workflow with proper target namespacing and standard installation locations.

**Pattern Identification**:
- `install(EXPORT ...)` command following target installation
- FILE parameter specifying .cmake configuration file name
- NAMESPACE parameter providing target prefixing for imported targets
- DESTINATION parameter targeting standard cmake package directory using PROJECT_NAME
- Export name matching the EXPORT parameter from target installation

**Source Example**:
```cmake
install(
    EXPORT projectTargets
    FILE projectConfig.cmake
    NAMESPACE project::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}
)
```

**Exceptional Cases**: 
- **Exception Description**: Package export configuration with custom destination paths or non-standard naming conventions that deviate from cmake package discovery conventions.
- **Exception Pattern Identification**:
  - Custom DESTINATION paths that don't follow ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME} pattern
  - FILE names that don't follow <ProjectName>Config.cmake convention
  - NAMESPACE that doesn't match project naming conventions
- **Exception Source Example**:
```cmake
install(
    EXPORT projectTargets
    FILE CustomName.cmake
    NAMESPACE Custom::
    DESTINATION lib/custom-cmake
)
```
- **Exception Handler Reference**: NONE (no specific pattern exists for non-standard configurations)
