# Build Assessment - WJElement CMake Build System

This document provides a comprehensive analysis of the WJElement project's CMake build system, documenting its structure, dependencies, and build semantics according to the patterns defined in `build-interpretation-runbook.cmake.md`.

## Main Project Configuration

### [BAP-CMAKE-STRUCTURE-001] Project Declaration and CMake Version
- **Description**: Establishes the WJElement project identity and specifies minimum CMake 2.6 compatibility requirement. The project declaration defines the name as "WJElement" and the version requirement ensures compatibility with older CMake installations.

- **Source Code Snippet**:
```cmake
project(WJElement)
cmake_minimum_required(VERSION 2.6)
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 1-2
- **References**: [IP-CMAKE-STRUCTURE-001], [IP-CMAKE-STRUCTURE-002]

### [BAP-CMAKE-STRUCTURE-002] Standard Module Inclusion for Feature Detection
- **Description**: Includes essential CMake modules for system capability detection. CheckIncludeFiles enables header availability testing and CheckSymbolExists enables function availability testing, providing the foundation for conditional compilation based on system features.

- **Source Code Snippet**:
```cmake
include(CheckIncludeFiles)
include(CheckSymbolExists)
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 3-4
- **References**: [IP-CMAKE-STRUCTURE-003]

## Build Configuration Options

### [BAP-CMAKE-CONFIG-001] Static/Shared Library Build Option
- **Description**: Provides user-configurable option to build as static libraries instead of shared libraries. When enabled, sets BUILD_SHARED_LIBS to OFF and defines COMPILE_AS_STATIC preprocessor macro to enable conditional compilation for static library behavior.

- **Source Code Snippet**:
```cmake
option(STATIC_LIB "Compile as static libraries" OFF)

if(STATIC_LIB)
  set(BUILD_SHARED_LIBS OFF)
  add_definitions(-DCOMPILE_AS_STATIC)
else(STATIC_LIB)
  set(BUILD_SHARED_LIBS ON)
endif(STATIC_LIB)
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 6-12
- **References**: [IP-CMAKE-CONFIG-001]

### [BAP-CMAKE-CONFIG-002] Integer Type Distinction Feature Toggle
- **Description**: Provides build-time feature toggle to enable distinction between integer and non-integer numbers in JSON processing. When enabled, defines WJE_DISTINGUISH_INTEGER_TYPE preprocessor macro to modify runtime behavior.

- **Source Code Snippet**:
```cmake
option(DISTINGUISH_INTEGERS "Distinguish between integer and non-integer numbers" OFF)
if(DISTINGUISH_INTEGERS)
  add_definitions(-DWJE_DISTINGUISH_INTEGER_TYPE)
endif()
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 14-17
- **References**: [IP-CMAKE-CONFIG-002]

### [BAP-CMAKE-CONFIG-003] Linux-Specific GNU Source Definition
- **Description**: Applies Linux-specific preprocessor definition to enable GNU extensions when building on Linux systems. This enables access to GNU-specific functionality that may be required for full feature support.

- **Source Code Snippet**:
```cmake
if("${CMAKE_SYSTEM}" MATCHES "Linux")
  add_definitions(-D_GNU_SOURCE)
endif()
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 19-21
- **References**: [IP-CMAKE-CONFIG-003]

### [BAP-CMAKE-CONFIG-004] Debug Build Configuration with GCC Optimization
- **Description**: Configures debug-specific compilation settings including DEBUG preprocessor definition and GCC-specific debug optimization flags. When building in debug mode with GCC, disables optimization (-O0) and enables maximum debugging information (-g3 -ggdb3 -gdwarf-2).

- **Source Code Snippet**:
```cmake
if(CMAKE_BUILD_TYPE MATCHES Debug)
  add_definitions(-DDEBUG)
  option(SHOWNOTES "Show preprocessor notes" OFF)

  if(CMAKE_COMPILER_IS_GNUCC)
	# GCC specific debug options
	set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -O0 -g3 -ggdb3 -gdwarf-2")
	set(AVOID_VERSION -avoid-version)
  endif(CMAKE_COMPILER_IS_GNUCC)
endif(CMAKE_BUILD_TYPE MATCHES Debug)
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 32-41
- **References**: [IP-CMAKE-CONFIG-004]

## Installation Directory Configuration

### [BAP-CMAKE-INSTALL-001] Custom Installation Directory Setup
- **Description**: Establishes custom installation directories with conditional defaults for headers, libraries, and binaries. Provides /usr/local as default prefix while allowing user override, and creates separate destination variables for different component types.

- **Source Code Snippet**:
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

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 23-31
- **References**: [IP-CMAKE-INSTALL-003]

## Feature Detection and Configuration

### [BAP-CMAKE-FEATURE-001] String Header Detection
- **Description**: Performs compile-time detection of string.h header availability and strcasecmp function availability to enable conditional compilation based on system capabilities. Sets HAVE_STRING_H and HAVE_STRCASECMP variables for use in preprocessor conditionals.

- **Source Code Snippet**:
```cmake
add_definitions(-DHAVE_CONFIG_H)

check_include_files(string.h HAVE_STRING_H)
check_symbol_exists(strcasecmp "strings.h" HAVE_STRCASECMP)
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 43-46
- **References**: [IP-CMAKE-FEATURE-001]

### [BAP-CMAKE-FEATURE-002] Regular Expression Support Detection
- **Description**: Detects regex.h header availability and provides comprehensive warning messages when regex support is unavailable. This feature detection enables JSON schema pattern validation, patternProperties, and format validation functionality.

- **Source Code Snippet**:
```cmake
check_include_files(regex.h HAVE_REGEX_H)
if(HAVE_REGEX_H)
	add_definitions(-DHAVE_REGEX_H)
else(HAVE_REGEX_H)
	message("***  WARNING: GNU C regex library not found.")
	message("     WJESchemaValidate() will not support:")
	message("         pattern")
	message("         patternProperties")
	message("         format")
endif(HAVE_REGEX_H)
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 48-58
- **References**: [IP-CMAKE-FEATURE-001]

## Runtime Path Configuration

### [BAP-CMAKE-RPATH-001] Comprehensive RPATH Configuration
- **Description**: Configures complete RPATH behavior for proper shared library discovery at runtime in both development and installed environments. Enables build-time RPATH, configures install-time paths to library destination, and automatically includes dependency paths.

- **Source Code Snippet**:
```cmake
# use, i.e. don't skip the full RPATH for the build tree
SET(CMAKE_SKIP_BUILD_RPATH  FALSE)
# when building, don't use the install RPATH already
# (but later on when installing)
SET(CMAKE_BUILD_WITH_INSTALL_RPATH FALSE)
# the RPATH to be used when installing
SET(CMAKE_INSTALL_RPATH ${LIB_DEST_DIR})
# add the automatically determined parts of the RPATH
# which point to directories outside the build tree to the install RPATH
SET(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 60-69
- **References**: [IP-CMAKE-RPATH-001]

## Testing Framework Configuration

### [BAP-CMAKE-TEST-001] Testing Framework Activation
- **Description**: Activates CMake's built-in testing framework to enable test definition and execution capabilities throughout the project. This establishes the foundation for comprehensive unit testing infrastructure.

- **Source Code Snippet**:
```cmake
ENABLE_TESTING(1)
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 71
- **References**: [IP-CMAKE-TEST-001]

## Documentation and Project Structure

### [BAP-CMAKE-INSTALL-002] Documentation Directory Installation
- **Description**: Installs the entire documentation directory to a project-specific location under the installation prefix. This ensures comprehensive documentation is packaged with the installed software for end-user reference.

- **Source Code Snippet**:
```cmake
INSTALL(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/documentation/
	DESTINATION doc/wjelement/
)
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 73-75
- **References**: [IP-CMAKE-INSTALL-007]

### [BAP-CMAKE-STRUCTURE-003] Global Include Directory and Subdirectory Delegation
- **Description**: Establishes global include directories that apply to all targets and delegates build configuration to specialized subdirectories. This provides project-wide header accessibility and enables modular build organization.

- **Source Code Snippet**:
```cmake
include_directories(include)
add_subdirectory(src)
add_subdirectory(include)
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 77-79
- **References**: [IP-CMAKE-STRUCTURE-005], [IP-CMAKE-STRUCTURE-006]

## Package Integration

### [BAP-CMAKE-PKG-001] pkg-config Integration Configuration
- **Description**: Generates pkg-config .pc file to enable easy discovery and linking by other projects using the pkg-config system. Configures library dependencies, link flags, and compiler flags for downstream project consumption.

- **Source Code Snippet**:
```cmake
# pkg-config .pc
SET(PKG_CONFIG_REQUIRES glib-2.0)
SET(PKG_CONFIG_LIBDIR
    ${LIB_DEST_DIR}
)
SET(PKG_CONFIG_INCLUDEDIR
	${INC_DEST_DIR}
)
SET(PKG_CONFIG_LIBS
    "-L\${libdir} -lwjelement -lwjreader -lwjwriter"
)
SET(PKG_CONFIG_CFLAGS
    "-I\${includedir}"
)
CONFIGURE_FILE(
  "${CMAKE_CURRENT_SOURCE_DIR}/pkg-config.pc.cmake"
  "${CMAKE_CURRENT_BINARY_DIR}/wjelement.pc"
)
INSTALL(FILES "${CMAKE_CURRENT_BINARY_DIR}/wjelement.pc"
        DESTINATION ${LIB_DEST_DIR}/pkgconfig)
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 81-98
- **References**: [IP-CMAKE-PKG-001]

## Build System Utilities

### [BAP-CMAKE-UTIL-001] Uninstall Target Creation
- **Description**: Creates a custom uninstall target by configuring an uninstall script template to enable removal of installed files. This provides functionality not included in CMake by default, allowing users to cleanly remove the installed software.

- **Source Code Snippet**:
```cmake
# uninstall target
configure_file(
    "${CMAKE_CURRENT_SOURCE_DIR}/cmake_uninstall.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake"
    IMMEDIATE @ONLY)

add_custom_target(uninstall
    COMMAND ${CMAKE_COMMAND} -P ${CMAKE_CURRENT_BINARY_DIR}/cmake_uninstall.cmake)
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 100-107
- **References**: [IP-CMAKE-UTIL-001]

## Packaging Configuration

### [BAP-CMAKE-PACK-001] CPack Debian Package Configuration
- **Description**: Configures CPack for generating Debian packages with comprehensive package metadata, automatic dependency detection, and version management. This enables distribution packaging for Debian-based systems with proper shared library dependency management.

- **Source Code Snippet**:
```cmake
SET(MAJOR_VERSION 1)
SET(MINOR_VERSION 0)
SET(PATCH_VERSION 1)
IF(EXISTS "${CMAKE_ROOT}/Modules/CPack.cmake")

INCLUDE (InstallRequiredSystemLibraries)

SET (CPACK_SET_DESTDIR "on")
SET (CPACK_PACKAGING_INSTALL_PREFIX "/tmp")
SET (CPACK_GENERATOR "DEB")

SET (CPACK_DEBIAN_PACKAGE_PRIORITY "extra")
SET (CPACK_DEBIAN_PACKAGE_SECTION "libs")
SET (CPACK_DEBIAN_ARCHITECTURE ${CMAKE_SYSTEM_PROCESSOR})
# autogenerate dependency information
set (CPACK_DEBIAN_PACKAGE_SHLIBDEPS ON)

SET (CPACK_PACKAGE_DESCRIPTION "Short description")
SET (CPACK_PACKAGE_DESCRIPTION_SUMMARY "Long description")
SET (CPACK_PACKAGE_VENDOR "Titan Lien (digbil)")
SET (CPACK_PACKAGE_CONTACT "titan@digbil.com")
SET (CPACK_PACKAGE_VERSION_MAJOR "${MAJOR_VERSION}")
SET (CPACK_PACKAGE_VERSION_MINOR "${MINOR_VERSION}")
SET (CPACK_PACKAGE_VERSION_PATCH "${PATCH_VERSION}")

SET (CPACK_PACKAGE_FILE_NAME "${CMAKE_PROJECT_NAME}_${MAJOR_VERSION}.${MINOR_VERSION}.${CPACK_PACKAGE_VERSION_PATCH}")
SET (CPACK_SOURCE_PACKAGE_FILE_NAME "${CMAKE_PROJECT_NAME}_${MAJOR_VERSION}.${MINOR_VERSION}.${CPACK_PACKAGE_VERSION_PATCH}")

SET (CPACK_COMPONENTS_ALL Libraries ApplicationData)
INCLUDE (CPack)
ENDIF(EXISTS "${CMAKE_ROOT}/Modules/CPack.cmake")
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 109-143
- **References**: [IP-CMAKE-PACK-001]

## Library Component Definitions

### [BAP-CMAKE-TARGET-001] XPL Utility Library Definition
- **Description**: Defines the XPL utility library from a single source file with complete version configuration and installation rules. This foundational library provides cross-platform utility functions used by other WJElement components.

- **Source Code Snippet**:
```cmake
add_library(xpl
	xpl.c
)

target_link_libraries(xpl
	${ALL_LIBS}
)

SET_TARGET_PROPERTIES(
	xpl
	PROPERTIES
	VERSION 1.0.0
	SOVERSION 1
	INSTALL_NAME_DIR "${LIB_DEST_DIR}"
)

install(TARGETS xpl
	LIBRARY DESTINATION ${LIB_DEST_DIR}
	ARCHIVE DESTINATION ${LIB_DEST_DIR}
	)
```

- **Path to Source File**: src/lib/CMakeLists.txt
- **Line Number in Source File**: 1-21
- **References**: [IP-CMAKE-TARGET-001], [IP-CMAKE-TARGET-004], [IP-CMAKE-TARGET-005]

### [BAP-CMAKE-TARGET-002] WJReader Library Definition
- **Description**: Defines the WJReader JSON parsing library with dependencies on the XPL utility library. Includes complete version configuration and installation rules for both shared and static library components.

- **Source Code Snippet**:
```cmake
add_library(wjreader
	wjreader.c
)

target_link_libraries(wjreader
	xpl
	${ALL_LIBS}
)

SET_TARGET_PROPERTIES(
	wjreader
	PROPERTIES
	VERSION 1.0.0
	SOVERSION 1
	INSTALL_NAME_DIR "${LIB_DEST_DIR}"
)

install(TARGETS wjreader
	LIBRARY DESTINATION ${LIB_DEST_DIR}
	ARCHIVE DESTINATION ${LIB_DEST_DIR}
	)
```

- **Path to Source File**: src/wjreader/CMakeLists.txt
- **Line Number in Source File**: 1-22
- **References**: [IP-CMAKE-TARGET-001], [IP-CMAKE-TARGET-004], [IP-CMAKE-TARGET-005]

### [BAP-CMAKE-TARGET-003] WJWriter Library Definition
- **Description**: Defines the WJWriter JSON generation library with dependencies on the XPL utility library. Includes complete version configuration and installation rules for both shared and static library components.

- **Source Code Snippet**:
```cmake
add_library(wjwriter
	wjwriter.c
)

target_link_libraries(wjwriter
	xpl
	${ALL_LIBS}
)

SET_TARGET_PROPERTIES(
	wjwriter
	PROPERTIES
	VERSION 1.0.0
	SOVERSION 1
	INSTALL_NAME_DIR "${LIB_DEST_DIR}"
)

install(TARGETS wjwriter
    LIBRARY DESTINATION ${LIB_DEST_DIR}
    ARCHIVE DESTINATION ${LIB_DEST_DIR}
    )
```

- **Path to Source File**: src/wjwriter/CMakeLists.txt
- **Line Number in Source File**: 1-24
- **References**: [IP-CMAKE-TARGET-001], [IP-CMAKE-TARGET-004], [IP-CMAKE-TARGET-005]

### [BAP-CMAKE-TARGET-004] WJElement Core Library Definition
- **Description**: Defines the main WJElement library with multiple source files including JSON element manipulation, searching, type handling, schema validation, and hash functionality. Links to both reader and writer libraries plus XPL utilities to provide comprehensive JSON processing capabilities.

- **Source Code Snippet**:
```cmake
add_library(wjelement
	element.c
	element.h
	search.c
	types.c
	schema.c
	hash.c
)

target_link_libraries(wjelement
	wjreader
	wjwriter
	xpl
	${ALL_LIBS}
)

SET_TARGET_PROPERTIES(
	wjelement
	PROPERTIES
	VERSION 1.0.0
	SOVERSION 1
	INSTALL_NAME_DIR "${LIB_DEST_DIR}"
)

install(TARGETS wjelement
	LIBRARY DESTINATION ${LIB_DEST_DIR}
	ARCHIVE DESTINATION ${LIB_DEST_DIR}
	)
```

- **Path to Source File**: src/wjelement/CMakeLists.txt
- **Line Number in Source File**: 1-30
- **References**: [IP-CMAKE-TARGET-001], [IP-CMAKE-TARGET-004], [IP-CMAKE-TARGET-005]

## Test Infrastructure

### [BAP-CMAKE-TARGET-005] Unit Test Executable Definition
- **Description**: Defines the comprehensive unit test executable with dependencies on all WJElement libraries including threading support. This test executable serves as the foundation for extensive automated testing coverage.

- **Source Code Snippet**:
```cmake
# unit tests
add_executable(wjeunit
	wjeunit.c
)

target_link_libraries(wjeunit
	wjreader
	wjwriter
	wjelement
	xpl
	${PTHREAD_LIBS}
	${ALL_LIBS}
)
```

- **Path to Source File**: src/wjelement/CMakeLists.txt
- **Line Number in Source File**: 32-44
- **References**: [IP-CMAKE-TARGET-006], [IP-CMAKE-LINKING-001]

### [BAP-CMAKE-TEST-002] Comprehensive Unit Test Suite Definition
- **Description**: Defines extensive unit test suite with 16 individual test cases covering all major WJElement functionality including self-reference handling, element names, offsets, lists, paths, value operations, conditionals, defaults, formatting, large document handling, and schema validation. Uses cross-platform executable paths for compatibility.

- **Source Code Snippet**:
```cmake
# The use of ${EXECUTABLE_OUTPUT_PATH} is required for windows
add_test(WJElement:SelfReference		${EXECUTABLE_OUTPUT_PATH}/wjeunit self			)
add_test(WJElement:ElementNames			${EXECUTABLE_OUTPUT_PATH}/wjeunit names			)
add_test(WJElement:Offsets				${EXECUTABLE_OUTPUT_PATH}/wjeunit offsets		)
add_test(WJElement:Lists				${EXECUTABLE_OUTPUT_PATH}/wjeunit lists			)
add_test(WJElement:Paths				${EXECUTABLE_OUTPUT_PATH}/wjeunit paths			)
add_test(WJElement:GetValues			${EXECUTABLE_OUTPUT_PATH}/wjeunit getvalue		)
add_test(WJElement:GetMultipleValues	${EXECUTABLE_OUTPUT_PATH}/wjeunit getvalues		)
add_test(WJElement:SetValues			${EXECUTABLE_OUTPUT_PATH}/wjeunit setvalue		)
add_test(WJElement:SetMultipleValues	${EXECUTABLE_OUTPUT_PATH}/wjeunit setvalues		)
add_test(WJElement:NewValues			${EXECUTABLE_OUTPUT_PATH}/wjeunit newvalue		)
add_test(WJElement:NewMultipleValues	${EXECUTABLE_OUTPUT_PATH}/wjeunit newvalues		)
add_test(WJElement:PutValues			${EXECUTABLE_OUTPUT_PATH}/wjeunit putvalue		)
add_test(WJElement:PutMultipleValues	${EXECUTABLE_OUTPUT_PATH}/wjeunit putvalues		)
add_test(WJElement:Append				${EXECUTABLE_OUTPUT_PATH}/wjeunit append		)
add_test(WJElement:Conditions			${EXECUTABLE_OUTPUT_PATH}/wjeunit conditions	)
add_test(WJElement:Optionals			${EXECUTABLE_OUTPUT_PATH}/wjeunit optionals		)
add_test(WJElement:Defaults				${EXECUTABLE_OUTPUT_PATH}/wjeunit defaults		)
add_test(WJElement:FormatStr			${EXECUTABLE_OUTPUT_PATH}/wjeunit formatstr		)
add_test(WJElement:BigDoc				${EXECUTABLE_OUTPUT_PATH}/wjeunit bigdoc		)
add_test(WJElement:RealBigDoc			${EXECUTABLE_OUTPUT_PATH}/wjeunit realbigdoc	)
add_test(WJElement:RealBigDoc2			${EXECUTABLE_OUTPUT_PATH}/wjeunit realbigdoc2	)
add_test(WJElement:Schema				${EXECUTABLE_OUTPUT_PATH}/wjeunit schema		)
```

- **Path to Source File**: src/wjelement/CMakeLists.txt
- **Line Number in Source File**: 46-69
- **References**: [IP-CMAKE-TEST-002]

## Command Line Interface

### [BAP-CMAKE-TARGET-006] Command Line Interface Executable
- **Description**: Defines the command line interface executable with multiple source files and custom compiler flags. Links to the main WJElement library to provide command-line access to JSON processing functionality.

- **Source Code Snippet**:
```cmake
add_executable(wjecli wjecli.c wjecli.h cmds.c)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${ALL_CFLAGS} ${PTHREAD_CFLAGS} ${OPENSSL_CFLAGS}")

target_link_libraries(wjecli
	wjelement
)

install(TARGETS wjecli DESTINATION bin)
install(PROGRAMS wje DESTINATION bin)
```

- **Path to Source File**: src/cli/CMakeLists.txt
- **Line Number in Source File**: 1-12
- **References**: [IP-CMAKE-TARGET-006], [IP-CMAKE-COMPILE-001], [IP-CMAKE-INSTALL-005], [IP-CMAKE-INSTALL-006]

## Header File Management

### [BAP-CMAKE-FILE-001] Public Header Discovery and Installation
- **Description**: Uses glob patterns to automatically discover all header files in the include directory, filters out build-generated config.h file, and installs the remaining public headers. This provides automatic header management that adapts to header file additions or removals.

- **Source Code Snippet**:
```cmake
FILE(GLOB INCLUDES_HEADERS *.h )
LIST(REMOVE_ITEM INCLUDES_HEADERS ${PROJECT_SOURCE_DIR}/include/config.h)
INSTALL(FILES ${INCLUDES_HEADERS} DESTINATION include/)
```

- **Path to Source File**: include/CMakeLists.txt
- **Line Number in Source File**: 1-4
- **References**: [IP-CMAKE-FILE-001], [IP-CMAKE-FILE-002], [IP-CMAKE-INSTALL-004]

## Subdirectory Organization

### [BAP-CMAKE-STRUCTURE-004] Source Component Delegation
- **Description**: Delegates build configuration to all major source components using separate CMakeLists.txt files. This modular organization enables each component (utility libraries, core libraries, command line interface) to manage its own build configuration independently.

- **Source Code Snippet**:
```cmake
add_subdirectory(lib)
add_subdirectory(wjreader)
add_subdirectory(wjwriter)
add_subdirectory(wjelement)
add_subdirectory(cli)
```

- **Path to Source File**: src/CMakeLists.txt
- **Line Number in Source File**: 1-6
- **References**: [IP-CMAKE-STRUCTURE-006]
