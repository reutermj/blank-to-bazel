# Build Interpretation Runbook Proposal - CMake

This document proposes new interpretation patterns for understanding and analyzing CMake build systems based on analysis of the WJElement project. These patterns extend the existing runbook with additional constructs not previously covered.

## Configuration Management Patterns

### [PROPOSED-IP-CMAKE-CONFIG-001] Build Type Option Pattern
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

### [PROPOSED-IP-CMAKE-CONFIG-002] Feature Toggle Option Pattern
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

### [PROPOSED-IP-CMAKE-CONFIG-003] Platform-Specific Definition Pattern
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

### [PROPOSED-IP-CMAKE-CONFIG-004] Debug Build Configuration Pattern
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

### [PROPOSED-IP-CMAKE-FILE-001] File Pattern Discovery Pattern
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

### [PROPOSED-IP-CMAKE-FILE-002] Selective File List Filtering Pattern
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

## Installation Configuration Patterns

### [PROPOSED-IP-CMAKE-INSTALL-001] Custom Installation Directory Pattern
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

### [PROPOSED-IP-CMAKE-INSTALL-002] Direct File Installation Pattern
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

### [PROPOSED-IP-CMAKE-INSTALL-004] Executable Installation Pattern
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

### [PROPOSED-IP-CMAKE-INSTALL-005] Program Script Installation Pattern
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

### [PROPOSED-IP-CMAKE-INSTALL-007] Documentation Directory Installation Pattern
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

## Runtime Path Management Patterns

### [PROPOSED-IP-CMAKE-RPATH-001] Comprehensive RPATH Configuration Pattern
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

## Feature Detection Patterns

### [PROPOSED-IP-CMAKE-FEATURE-001] System Capability Detection Pattern
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

## Compilation Configuration Patterns

### [PROPOSED-IP-CMAKE-COMPILE-001] Custom Compiler Flags Pattern
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

## Executable Target Patterns

### [PROPOSED-IP-CMAKE-TARGET-006] Basic Executable Target Pattern
- **Pattern Description**: Defines executable targets from source files. This pattern creates binary executables that can be built and potentially installed.

- **Pattern Identification**:
  - `add_executable()` calls specifying target name and source files
  - Source files typically include implementation files (.c, .cpp) and may include headers
  - Target names often reflect the executable's purpose

- **Source Example**:
```cmake
add_executable(mycli main.c cli.h commands.c)
```

- **Exceptional Cases**: None.

### [PROPOSED-IP-CMAKE-TARGET-007] Unit Test Executable Pattern
- **Pattern Description**: Defines executable targets specifically for unit testing. This pattern creates test executables with naming conventions that indicate their testing purpose.

- **Pattern Identification**:
  - `add_executable()` calls with names containing "test", "unit", or similar
  - Target names that clearly indicate testing purpose
  - Source files typically focused on test implementation

- **Source Example**:
```cmake
add_executable(mytest
	test.c
)
```

- **Exceptional Cases**: None.

## Target Linking Patterns

### [PROPOSED-IP-CMAKE-LINKING-001] Comprehensive Library Linking Pattern
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

## Testing Framework Patterns

### [PROPOSED-IP-CMAKE-TEST-001] Testing Framework Activation Pattern
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

### [PROPOSED-IP-CMAKE-TEST-002] Individual Test Definition Pattern
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

## Package Integration Patterns

### [PROPOSED-IP-CMAKE-PKG-001] pkg-config Integration Pattern
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

### [PROPOSED-IP-CMAKE-UTIL-001] Uninstall Target Pattern
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

### [PROPOSED-IP-CMAKE-PACK-001] CPack Debian Package Pattern
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

## Project Structure Organization Patterns

### [PROPOSED-IP-CMAKE-STRUCTURE-006] Global Include Directory Configuration Pattern
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

### [PROPOSED-IP-CMAKE-STRUCTURE-007] Subdirectory Delegation Pattern
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
