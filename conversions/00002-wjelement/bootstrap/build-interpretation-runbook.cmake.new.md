# Build Interpretation Runbook - CMake

This document provides interpretation patterns for understanding and analyzing CMake build systems based on analysis of multiple projects including Parson JSON library and WJElement.

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

- **Exceptional Cases**: None.

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

- **Exceptional Cases**: None.

### [IP-CMAKE-STRUCTURE-003] Standard Module Inclusion Pattern
- **Pattern Description**: Incorporates CMake standard modules to extend build system capabilities with predefined functionality. This pattern indicates reliance on CMake's standard library for common build tasks.

- **Pattern Identification**:
  - `include(<module_name>)` calls for standard CMake modules
  - Common modules: GNUInstallDirs, FindPackageHandleStandardArgs, etc.

- **Source Example**:
```cmake
include (GNUInstallDirs)
```

- **Exceptional Cases**: None.

### [IP-CMAKE-STRUCTURE-004] Centralized Version Management Pattern
- **Pattern Description**: Establishes project version information in variables for reuse across multiple build components. This pattern ensures version consistency and simplifies version maintenance.

- **Pattern Identification**:
  - `set(<PROJECT>_VERSION <version>)` variable definition
  - Version variable referenced in target properties or other contexts

- **Source Example**:
```cmake
set(PROJECT_VERSION 1.2.3)
```

- **Exceptional Cases**: None.

### [IP-CMAKE-STRUCTURE-005] Global Include Directory Configuration Pattern
- **Pattern Description**: Establishes global include directories that apply to all targets in the project. This pattern provides project-wide header file accessibility without requiring per-target include path specification.

- **Pattern Identification**:
  - `include_directories()` calls for global include path setup
  - Typically specifies project's main include directory
  - Affects all targets defined after the call

- **Source Example**:
```cmake
include_directories(include)
```

- **Exceptional Cases**: None.

### [IP-CMAKE-STRUCTURE-006] Subdirectory Delegation Pattern
- **Pattern Description**: Delegates build configuration to subdirectories using separate CMakeLists.txt files. This pattern enables modular build organization where each component manages its own build configuration.

- **Pattern Identification**:
  - Multiple `add_subdirectory()` calls delegating to component-specific directories
  - Subdirectory structure typically mirrors logical project organization
  - Each subdirectory contains its own CMakeLists.txt file
  - Enables separation of concerns in build configuration

- **Source Example**:
```cmake
add_subdirectory(src)
add_subdirectory(include)
```

- **Exceptional Cases**: None.

## Configuration Management Patterns

### [IP-CMAKE-CONFIG-001] Build Type Option Pattern
- **Pattern Description**: Provides user-configurable build options that affect library type (static vs shared) and set corresponding preprocessor definitions. This pattern enables conditional compilation based on user-selected build configurations.

- **Pattern Identification**: 
  - `option()` command defining build configuration choices
  - Conditional logic using `if()` statements based on option values
  - `set(BUILD_SHARED_LIBS ...)` calls controlling library build type
  - `add_definitions()` calls setting preprocessor macros based on options

- **Source Example**:
```cmake
option(STATIC_LIB "Compile as static libraries" OFF)

if(STATIC_LIB)
  set(BUILD_SHARED_LIBS OFF)
  add_definitions(-DCOMPILE_AS_STATIC)
else(STATIC_LIB)
  set(BUILD_SHARED_LIBS ON)
endif(STATIC_LIB)
```

- **Exceptional Cases**: None.

### [IP-CMAKE-CONFIG-002] Feature Toggle Option Pattern
- **Pattern Description**: Provides build-time feature toggles that enable or disable specific functionality through preprocessor definitions. This pattern allows conditional compilation of features without changing source code.

- **Pattern Identification**:
  - `option()` command defining feature toggle
  - Conditional `add_definitions()` calls setting feature-specific preprocessor macros
  - Descriptive option names indicating functional behavior changes

- **Source Example**:
```cmake
option(FEATURE_TOGGLE "Enable specific feature functionality" OFF)
if(FEATURE_TOGGLE)
  add_definitions(-DFEATURE_ENABLED)
endif()
```

- **Exceptional Cases**: None.

### [IP-CMAKE-CONFIG-003] Platform-Specific Definition Pattern
- **Pattern Description**: Applies platform-specific preprocessor definitions based on the target system. This pattern enables platform-specific functionality without manual configuration.

- **Pattern Identification**:
  - `CMAKE_SYSTEM` variable matching against platform names
  - Conditional `add_definitions()` calls for platform-specific macros
  - Platform names typically "Linux", "Windows", "Darwin", etc.

- **Source Example**:
```cmake
if("${CMAKE_SYSTEM}" MATCHES "Linux")
  add_definitions(-D_GNU_SOURCE)
endif()
```

- **Exceptional Cases**: None.

### [IP-CMAKE-CONFIG-004] Debug Build Configuration Pattern
- **Pattern Description**: Configures debug-specific compilation settings including preprocessor definitions and compiler-specific optimization flags when building in debug mode. This pattern provides enhanced debugging capabilities and disables optimizations.

- **Pattern Identification**:
  - `CMAKE_BUILD_TYPE` variable matching "Debug"
  - `add_definitions()` calls for debug-specific macros
  - Compiler-specific flag detection using `CMAKE_COMPILER_IS_*` variables
  - `CMAKE_C_FLAGS` or `CMAKE_CXX_FLAGS` modification for debug options

- **Source Example**:
```cmake
if(CMAKE_BUILD_TYPE MATCHES Debug)
  add_definitions(-DDEBUG)
  
  if(CMAKE_COMPILER_IS_GNUCC)
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -O0 -g3 -ggdb3 -gdwarf-2")
  endif(CMAKE_COMPILER_IS_GNUCC)
endif(CMAKE_BUILD_TYPE MATCHES Debug)
```

- **Exceptional Cases**: None.

## File Management Patterns

### [IP-CMAKE-FILE-001] File Pattern Discovery Pattern
- **Pattern Description**: Uses glob patterns to automatically discover files matching specific criteria in directories. This pattern eliminates the need to manually list files and automatically adapts to file additions or removals.

- **Pattern Identification**:
  - `FILE(GLOB ...)` commands with pattern matching
  - Variable assignment to store discovered file lists
  - Pattern typically targets specific file extensions (*.h, *.c, *.cpp, etc.)

- **Source Example**:
```cmake
FILE(GLOB PROJECT_HEADERS *.h)
```

- **Exceptional Cases**: None.

### [IP-CMAKE-FILE-002] Selective File List Filtering Pattern
- **Pattern Description**: Removes specific files from previously created file lists to exclude unwanted items from processing. This pattern enables fine-grained control over which files are included in operations like installation.

- **Pattern Identification**:
  - `LIST(REMOVE_ITEM ...)` calls operating on file lists
  - Typically removes build-generated files, private headers, or configuration files
  - Often follows `FILE(GLOB ...)` commands

- **Source Example**:
```cmake
LIST(REMOVE_ITEM PROJECT_HEADERS ${PROJECT_SOURCE_DIR}/include/config.h)
```

- **Exceptional Cases**: None.

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

- **Exceptional Cases**: None.

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

- **Exceptional Cases**: None.

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

- **Exceptional Cases**: None.

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

- **Exceptional Cases**: None.

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

- **Exceptional Cases**: None.

### [IP-CMAKE-TARGET-006] Executable Target Definition Pattern
- **Pattern Description**: Defines executable targets from source files. This pattern creates binary executables that can be built and potentially installed, including general-purpose executables, command-line tools, and test executables.

- **Pattern Identification**:
  - `add_executable()` calls specifying target name and source files
  - Source files typically include implementation files (.c, .cpp) and may include headers
  - Target names often reflect the executable's purpose

- **Source Example**:
```cmake
add_executable(mycli main.c cli.h commands.c)
add_executable(mytest test.c)
```

- **Exceptional Cases**: 
  - **Exception Description**: Test executable targets that are specifically designed for unit testing may follow naming conventions that clearly indicate their testing purpose and use specialized source file organization.
  - **Exception Pattern Identification**:
    - `add_executable()` calls with names containing "test", "unit", or similar
    - Target names that clearly indicate testing purpose
    - Source files typically focused on test implementation
  - **Exception Source Example**:
```cmake
add_executable(unit_test
	unit_tests.c
	test_helpers.c
)
```
  - **Exception Handler Reference**: [IP-CMAKE-TEST-002]

## Target Linking Patterns

### [IP-CMAKE-LINKING-001] Comprehensive Library Linking Pattern
- **Pattern Description**: Links targets to multiple project libraries and system dependencies including threading libraries. This pattern establishes complete dependency chains for complex targets like test executables.

- **Pattern Identification**:
  - `target_link_libraries()` linking to multiple project libraries
  - Threading library linkage (PTHREAD_LIBS, pthread, etc.)
  - System library linkage through variable expansion (${ALL_LIBS})
  - Comprehensive dependency specification for full functionality

- **Source Example**:
```cmake
target_link_libraries(mytest
	myreader
	mywriter
	mycore
	myutil
	${PTHREAD_LIBS}
	${ALL_LIBS}
)
```

- **Exceptional Cases**: None.

## Compilation Configuration Patterns

### [IP-CMAKE-COMPILE-001] Custom Compiler Flags Pattern
- **Pattern Description**: Modifies compiler flags by appending additional flags to the existing compilation settings. This pattern enables project-specific compilation options beyond the default CMake settings.

- **Pattern Identification**:
  - `set(CMAKE_C_FLAGS ...)` or `set(CMAKE_CXX_FLAGS ...)` calls appending to existing flags
  - Use of `${CMAKE_C_FLAGS}` or `${CMAKE_CXX_FLAGS}` to preserve existing settings
  - Variable expansion for flag collections (e.g., `${ALL_CFLAGS}`, `${PTHREAD_CFLAGS}`)

- **Source Example**:
```cmake
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${ALL_CFLAGS} ${PTHREAD_CFLAGS}")
```

- **Exceptional Cases**: None.

## Feature Detection Patterns

### [IP-CMAKE-FEATURE-001] System Capability Detection Pattern
- **Pattern Description**: Performs compile-time detection of system headers and functions to enable conditional compilation based on available system features. This pattern provides graceful degradation when system features are unavailable.

- **Pattern Identification**:
  - `check_include_files()` calls for header availability testing
  - `check_symbol_exists()` calls for function availability testing
  - Conditional `add_definitions()` based on detection results
  - Warning messages for missing optional features
  - Feature variable names typically follow HAVE_* naming convention

- **Source Example**:
```cmake
check_include_files(regex.h HAVE_REGEX_H)
if(HAVE_REGEX_H)
	add_definitions(-DHAVE_REGEX_H)
else(HAVE_REGEX_H)
	message("***  WARNING: Feature library not found.")
	message("     Some functionality will not be available:")
	message("         advanced pattern matching")
endif(HAVE_REGEX_H)
```

- **Exceptional Cases**: None.

## Runtime Path Management Patterns

### [IP-CMAKE-RPATH-001] Comprehensive RPATH Configuration Pattern
- **Pattern Description**: Configures complete RPATH behavior for proper library discovery at runtime, including build-time behavior, install-time paths, and automatic dependency inclusion. This pattern ensures shared libraries can be found in both development and deployed environments.

- **Pattern Identification**:
  - `CMAKE_SKIP_BUILD_RPATH` set to FALSE to enable build-time RPATH
  - `CMAKE_BUILD_WITH_INSTALL_RPATH` set to FALSE for proper build behavior
  - `CMAKE_INSTALL_RPATH` set to target library directory
  - `CMAKE_INSTALL_RPATH_USE_LINK_PATH` set to TRUE for automatic dependency paths

- **Source Example**:
```cmake
SET(CMAKE_SKIP_BUILD_RPATH  FALSE)
SET(CMAKE_BUILD_WITH_INSTALL_RPATH FALSE)
SET(CMAKE_INSTALL_RPATH ${LIB_DEST_DIR})
SET(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)
```

- **Exceptional Cases**: None.

## Testing Framework Patterns

### [IP-CMAKE-TEST-001] Testing Framework Activation Pattern
- **Pattern Description**: Activates CMake's built-in testing framework to enable test definition and execution capabilities. This pattern establishes the foundation for adding and running tests within the build system.

- **Pattern Identification**:
  - `ENABLE_TESTING()` call to activate CMake testing
  - Typically appears early in the main CMakeLists.txt file
  - May include optional parameter (e.g., `ENABLE_TESTING(1)`)

- **Source Example**:
```cmake
ENABLE_TESTING(1)
```

- **Exceptional Cases**: None.

### [IP-CMAKE-TEST-002] Individual Test Definition Pattern
- **Pattern Description**: Defines individual test cases using CMake's testing framework with descriptive names and cross-platform executable paths. This pattern creates specific test entries that can be executed by the test runner.

- **Pattern Identification**:
  - Multiple `add_test()` calls with descriptive test names
  - Use of `${EXECUTABLE_OUTPUT_PATH}` for cross-platform compatibility
  - Test names often following namespace:function patterns
  - Test executable calls with different command-line arguments

- **Source Example**:
```cmake
add_test(MyProject:BasicTest		${EXECUTABLE_OUTPUT_PATH}/mytest basic		)
add_test(MyProject:AdvancedTest		${EXECUTABLE_OUTPUT_PATH}/mytest advanced	)
add_test(MyProject:PerformanceTest	${EXECUTABLE_OUTPUT_PATH}/mytest performance)
```

- **Exceptional Cases**: None.

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

- **Exceptional Cases**: None.

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

- **Exceptional Cases**: None.

### [IP-CMAKE-INSTALL-003] Custom Installation Directory Pattern
- **Pattern Description**: Establishes custom installation directories with conditional defaults, allowing user override while providing fallback values. This pattern provides flexibility in installation layout while maintaining usability.

- **Pattern Identification**:
  - Conditional checks for existing `CMAKE_INSTALL_PREFIX` or custom directory variables
  - `set()` calls establishing default installation paths
  - Variable usage combining `CMAKE_INSTALL_PREFIX` with subdirectory paths

- **Source Example**:
```cmake
if(NOT CMAKE_INSTALL_PREFIX)
  set(CMAKE_INSTALL_PREFIX /usr/local)
endif(NOT CMAKE_INSTALL_PREFIX)
set(INC_DEST_DIR ${CMAKE_INSTALL_PREFIX}/include)
if(NOT LIB_DEST_DIR)
  set(LIB_DEST_DIR ${CMAKE_INSTALL_PREFIX}/lib)
endif(NOT LIB_DEST_DIR)
set(BIN_DEST_DIR ${CMAKE_INSTALL_PREFIX}/bin)
```

- **Exceptional Cases**: None.

### [IP-CMAKE-INSTALL-004] Direct File Installation Pattern
- **Pattern Description**: Installs specific files or file lists to designated installation directories. This pattern provides precise control over which files are installed and where they are placed.

- **Pattern Identification**:
  - `INSTALL(FILES ...)` commands specifying files or file variables
  - `DESTINATION` parameter defining installation location
  - Often used with file lists created by glob or manual specification

- **Source Example**:
```cmake
INSTALL(FILES ${PROJECT_HEADERS} DESTINATION include/)
```

- **Exceptional Cases**: None.

### [IP-CMAKE-INSTALL-005] Executable Installation Pattern
- **Pattern Description**: Installs executable targets to designated installation directories. This pattern enables deployment of built executables to system locations.

- **Pattern Identification**:
  - `install(TARGETS ...)` commands for executable targets
  - `DESTINATION` parameter typically specifying "bin" directory
  - Target names referencing executables created with `add_executable()`

- **Source Example**:
```cmake
install(TARGETS mycli DESTINATION bin)
```

- **Exceptional Cases**: None.

### [IP-CMAKE-INSTALL-006] Program Script Installation Pattern
- **Pattern Description**: Installs additional program files (scripts, helpers) that accompany the main executable. This pattern enables installation of non-compiled program components.

- **Pattern Identification**:
  - `install(PROGRAMS ...)` commands for script files
  - `DESTINATION` parameter typically specifying "bin" directory
  - Files are typically shell scripts, Python scripts, or other executable files

- **Source Example**:
```cmake
install(PROGRAMS myscript DESTINATION bin)
```

- **Exceptional Cases**: None.

### [IP-CMAKE-INSTALL-007] Documentation Directory Installation Pattern
- **Pattern Description**: Installs entire documentation directories to project-specific locations under the installation prefix. This pattern ensures documentation is properly packaged with the installed software.

- **Pattern Identification**:
  - `INSTALL(DIRECTORY ...)` commands for directory installation
  - Source directory typically named "documentation", "docs", or "doc"
  - Destination includes project name for organization

- **Source Example**:
```cmake
INSTALL(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/documentation/
	DESTINATION doc/myproject/
)
```

- **Exceptional Cases**: None.

## Package Integration Patterns

### [IP-CMAKE-PKG-001] pkg-config Integration Pattern
- **Pattern Description**: Generates pkg-config .pc files to enable easy discovery and linking by other projects using the pkg-config system. This pattern provides standard Unix-style package discovery.

- **Pattern Identification**:
  - Variables starting with `PKG_CONFIG_` for package metadata
  - `CONFIGURE_FILE()` call processing .pc.cmake template to .pc file
  - `INSTALL(FILES ...)` installing .pc file to pkgconfig directory
  - Template file with .pc.cmake extension

- **Source Example**:
```cmake
SET(PKG_CONFIG_REQUIRES glib-2.0)
SET(PKG_CONFIG_LIBDIR ${LIB_DEST_DIR})
SET(PKG_CONFIG_LIBS "-L\${libdir} -lmylib -lmyreader -lmywriter")
SET(PKG_CONFIG_CFLAGS "-I\${includedir}")

CONFIGURE_FILE(
  "${CMAKE_CURRENT_SOURCE_DIR}/pkg-config.pc.cmake"
  "${CMAKE_CURRENT_BINARY_DIR}/myproject.pc"
)
INSTALL(FILES "${CMAKE_CURRENT_BINARY_DIR}/myproject.pc"
        DESTINATION ${LIB_DEST_DIR}/pkgconfig)
```

- **Exceptional Cases**: None.

## Build System Utility Patterns

### [IP-CMAKE-UTIL-001] Uninstall Target Pattern
- **Pattern Description**: Creates a custom uninstall target by configuring an uninstall script template to enable removal of installed files. This pattern provides functionality not included in CMake by default.

- **Pattern Identification**:
  - `configure_file()` processing uninstall script template
  - Template file typically named cmake_uninstall.cmake.in
  - `add_custom_target()` creating "uninstall" target
  - Target execution using `${CMAKE_COMMAND} -P` for script execution

- **Source Example**:
```cmake
configure_file(
    "${CMAKE_CURRENT_SOURCE_DIR}/cmake_uninstall.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake"
    IMMEDIATE @ONLY)

add_custom_target(uninstall
    COMMAND ${CMAKE_COMMAND} -P ${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake)
```

- **Exceptional Cases**: None.

## Packaging Configuration Patterns

### [IP-CMAKE-PACK-001] CPack Debian Package Pattern
- **Pattern Description**: Configures CPack for generating Debian packages with comprehensive package metadata, dependency management, and component organization. This pattern enables distribution packaging for Debian-based systems.

- **Pattern Identification**:
  - Conditional inclusion based on CPack module availability
  - `INCLUDE(InstallRequiredSystemLibraries)` for system dependency inclusion
  - Variables starting with `CPACK_` for package configuration
  - `CPACK_GENERATOR` set to "DEB" for Debian packages
  - Debian-specific variables like `CPACK_DEBIAN_PACKAGE_*`
  - Version variables referencing project version components
  - `INCLUDE(CPack)` to activate packaging

- **Source Example**:
```cmake
IF(EXISTS "${CMAKE_ROOT}/Modules/CPack.cmake")
INCLUDE (InstallRequiredSystemLibraries)

SET (CPACK_GENERATOR "DEB")
SET (CPACK_DEBIAN_PACKAGE_SECTION "libs")
SET (CPACK_DEBIAN_ARCHITECTURE ${CMAKE_SYSTEM_PROCESSOR})
set (CPACK_DEBIAN_PACKAGE_SHLIBDEPS ON)

SET (CPACK_PACKAGE_VERSION_MAJOR "${MAJOR_VERSION}")
SET (CPACK_PACKAGE_VERSION_MINOR "${MINOR_VERSION}")

INCLUDE (CPack)
ENDIF(EXISTS "${CMAKE_ROOT}/Modules/CPack.cmake")
```

- **Exceptional Cases**: None.
