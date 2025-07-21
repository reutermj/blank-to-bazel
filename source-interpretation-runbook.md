# Source Interpretation Runbook Proposal

This document proposes interpretation patterns for understanding and analyzing build-relevant source code patterns based on analysis of the Parson JSON library project.

## Header File Organization Patterns

### [SIP-HEADERS-001] Include Guard Pattern
- **Pattern Description**: Standard C/C++ include guard pattern that prevents multiple inclusion of header files during compilation. This pattern indicates that the header file is designed for safe inclusion in multiple compilation units and is essential for proper C/C++ linking.

- **Pattern Identification**: 
  - `#ifndef <GUARD_NAME>` at the beginning of the header file
  - `#define <GUARD_NAME>` immediately following the ifndef
  - `#endif` at the end of the header file
  - Guard name typically relates to the header filename

- **Build Implications**: Header file can be safely included multiple times without redefinition errors. Build system must ensure header is available in include path for all compilation units that need it.

- **Source Example**:
```c
#ifndef my_library_h
#define my_library_h
/* header content */
#endif
```

- **Exceptional Cases**: None.

### [SIP-HEADERS-002] C++ Compatibility Wrapper Pattern
- **Pattern Description**: C++ compatibility guards that enable C headers to be included in C++ projects by providing C linkage specification. This pattern indicates the library is designed for cross-language usage between C and C++.

- **Pattern Identification**:
  - `#ifdef __cplusplus` conditional compilation directive
  - `extern "C" {` declaration following the ifdef
  - Corresponding closing `#ifdef __cplusplus` and `}` at end of header
  - Function declarations enclosed within the extern C block

- **Build Implications**: Library can be consumed by both C and C++ projects. Build system should support compilation with both C and C++ compilers for testing. Header installation must preserve C++ compatibility.

- **Source Example**:
```c
#ifdef __cplusplus
extern "C" {
#endif
/* function declarations */
#ifdef __cplusplus
}
#endif
```

- **Exceptional Cases**: None.

### [SIP-HEADERS-003] Header Version Constants Pattern
- **Pattern Description**: Version information embedded directly in header files using preprocessor constants. This pattern provides compile-time access to version information and enables version checking at both compile time and runtime.

- **Pattern Identification**:
  - `#define` directives for version components (MAJOR, MINOR, PATCH)
  - Version constants typically include project name prefix
  - May include string representation of complete version
  - Constants follow semantic versioning patterns

- **Build Implications**: Version information must be kept synchronized between build system version configuration and header constants. Build system should validate version consistency or generate header version constants from central version source.

- **Source Example**:
```c
#define MYLIB_VERSION_MAJOR 1
#define MYLIB_VERSION_MINOR 2
#define MYLIB_VERSION_PATCH 3
#define MYLIB_VERSION_STRING "1.2.3"
```

- **Exceptional Cases**: None.

### [SIP-HEADERS-004] Minimal Public Header Dependencies Pattern
- **Pattern Description**: Public header includes only essential standard library headers required for the public API interface. This pattern minimizes dependencies imposed on consuming projects and indicates disciplined API design.

- **Pattern Identification**:
  - Limited number of `#include <system_header>` directives in public header
  - Includes are typically for fundamental types (size_t, etc.)
  - No inclusion of complex or platform-specific headers
  - Comments may document the purpose of each inclusion

- **Build Implications**: Build system does not need to manage complex dependency chains for library consumption. Consuming projects have minimal transitive dependencies. Installation only needs to handle standard system headers.

- **Source Example**:
```c
#include <stddef.h>   /* size_t */
#include <stdio.h>    /* FILE */
```

- **Exceptional Cases**: None.

## Implementation File Organization Patterns

### [SIP-IMPL-001] Local Header Inclusion Pattern
- **Pattern Description**: Implementation file includes its corresponding public header file to ensure consistency between interface declarations and implementation definitions. This pattern provides compile-time validation of API conformance.

- **Pattern Identification**:
  - `#include "header.h"` directive near the top of implementation file
  - Header filename matches or relates to implementation filename
  - Relative path inclusion using quotes rather than angle brackets
  - Inclusion occurs before other implementation-specific includes

- **Build Implications**: Build system must ensure the header file is available in the include path during implementation compilation. Compilation will fail if interface and implementation don't match, providing early error detection.

- **Source Example**:
```c
#include "mylibrary.h"
```

- **Exceptional Cases**: None.

### [SIP-IMPL-002] Implementation Version Validation Pattern
- **Pattern Description**: Compile-time version consistency check between header and implementation files using preprocessor error directives. This pattern prevents version mismatches when header and implementation are compiled separately.

- **Pattern Identification**:
  - Implementation defines its own version constants
  - Preprocessor conditional that compares header and implementation versions
  - `#error` directive triggered when versions don't match
  - Version comparison typically checks all version components

- **Build Implications**: Build system must ensure version synchronization between header and implementation. Compilation will fail immediately if versions are inconsistent, preventing runtime version mismatch issues.

- **Source Example**:
```c
#define IMPL_VERSION_MAJOR 1
#define IMPL_VERSION_MINOR 2

#if (LIB_VERSION_MAJOR != IMPL_VERSION_MAJOR) || (LIB_VERSION_MINOR != IMPL_VERSION_MINOR)
#error "version mismatch between header and implementation"
#endif
```

- **Exceptional Cases**: None.

### [SIP-IMPL-003] Platform-Specific Compiler Configuration Pattern
- **Pattern Description**: Compiler-specific preprocessor directives that configure compilation behavior for different toolchains. This pattern indicates cross-platform support requirements and compiler-specific customization needs.

- **Pattern Identification**:
  - `#ifdef` directives testing for compiler-specific macros (_MSC_VER, __GNUC__, etc.)
  - Configuration of compiler-specific warnings, defines, or pragmas
  - Conditional definition of preprocessor symbols
  - Platform or compiler-specific workarounds

- **Build Implications**: Build system should support multiple compiler toolchains. Different compiler configurations may be needed for different target platforms. Testing should cover all supported compiler combinations.

- **Source Example**:
```c
#ifdef _MSC_VER
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif
#endif
```

- **Exceptional Cases**: None.

### [SIP-IMPL-004] Implementation Dependencies Pattern
- **Pattern Description**: Standard library includes required for implementation functionality beyond what's needed for the public API. This pattern defines the complete set of system dependencies needed for compilation.

- **Pattern Identification**:
  - Multiple `#include <standard_header>` directives
  - Headers for fundamental C library functions (stdio.h, stdlib.h, string.h, etc.)
  - Includes occur after local header inclusion
  - May include mathematical, I/O, or system-specific headers

- **Build Implications**: Build system must ensure all standard library dependencies are available on target platforms. Cross-compilation may need to verify standard library availability. These are typically satisfied by standard C library.

- **Source Example**:
```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <math.h>
```

- **Exceptional Cases**: None.

### [SIP-IMPL-005] Security Function Restriction Pattern
- **Pattern Description**: Preprocessor redefinitions that prevent use of potentially unsafe standard library functions by replacing them with compile-time errors. This pattern enforces secure coding practices at the source level.

- **Pattern Identification**:
  - `#ifdef` testing for potentially unsafe function names
  - `#undef` of the function name
  - `#define` redirecting function name to descriptive error message
  - Functions commonly restricted include strcpy, sprintf, scanf variants

- **Build Implications**: Build system compilation will fail if restricted functions are accidentally used, providing compile-time security enforcement. No additional build configuration needed - security is enforced at source level.

- **Source Example**:
```c
#ifdef strcpy
#undef strcpy
#endif
#define strcpy USE_SAFER_FUNCTION_INSTEAD
```

- **Exceptional Cases**: None.

### [SIP-IMPL-006] Configurable Implementation Parameters Pattern
- **Pattern Description**: Preprocessor constants with conditional compilation that allow customization of implementation behavior without source modification. This pattern enables build-time configuration of library parameters.

- **Pattern Identification**:
  - `#ifndef PARAMETER_NAME` conditional checks
  - `#define PARAMETER_NAME default_value` providing default values
  - Parameters typically control buffer sizes, formatting, or behavior
  - Parameter names often include project prefix

- **Build Implications**: Build system can optionally define these parameters to customize library behavior. Default values provide reasonable behavior without build system intervention. Custom builds can override parameters through compiler definitions.

- **Source Example**:
```c
#ifndef MYLIB_BUFFER_SIZE
#define MYLIB_BUFFER_SIZE 1024
#endif

#ifndef MYLIB_DEFAULT_FORMAT
#define MYLIB_DEFAULT_FORMAT "%s"
#endif
```

- **Exceptional Cases**: None.

## Test File Organization Patterns

### [SIP-TEST-001] Test Library Inclusion Pattern
- **Pattern Description**: Test source files include the library header to access the public API for testing purposes. This pattern demonstrates expected library usage and validates the public interface from a consumer perspective.

- **Pattern Identification**:
  - `#include "library_header.h"` in test files
  - Test file names often indicate their testing purpose
  - Header inclusion occurs early in test file
  - Same inclusion pattern as expected for consuming applications

- **Build Implications**: Build system must make library headers available to test compilation. Test compilation validates that public headers are correctly structured and accessible. Test targets should link against library implementation.

- **Source Example**:
```c
#include "mylibrary.h"
```

- **Exceptional Cases**: None.

### [SIP-TEST-002] Cross-Platform Test Configuration Pattern
- **Pattern Description**: Platform and compiler-specific configuration in test files to ensure tests can be compiled and run across different environments. This pattern handles platform differences in test execution environments.

- **Pattern Identification**:
  - Multiple `#ifdef` directives for different platforms or compilers
  - Compiler-specific warning suppressions
  - Platform-specific includes or definitions
  - Conditional compilation for different testing environments

- **Build Implications**: Build system should support test compilation on all target platforms. Test configuration should be validated across different compiler toolchains. Platform-specific test behaviors may need accommodation.

- **Source Example**:
```c
#ifdef _MSC_VER
#define _CRT_SECURE_NO_WARNINGS
#endif

#if defined(__APPLE__) && defined(__clang__)
#pragma clang diagnostic ignored "-Wdeprecated-declarations"
#endif
```

- **Exceptional Cases**: None.

### [SIP-TEST-003] Test Dependencies Pattern
- **Pattern Description**: Standard library includes required for test execution including assertion macros, I/O operations, and testing utilities. This pattern defines the testing environment requirements beyond the library being tested.

- **Pattern Identification**:
  - Includes for testing-related headers (assert.h, stdio.h for output)
  - Mathematical libraries for numeric testing (math.h)
  - String manipulation for test comparisons (string.h)
  - Memory management for test cleanup (stdlib.h)

- **Build Implications**: Build system must ensure test dependencies are available during test compilation. Test environment needs standard C library support. Test executables may have broader dependencies than the library itself.

- **Source Example**:
```c
#include <assert.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <math.h>
```

- **Exceptional Cases**: None.

### [SIP-TEST-004] Conditional Test Main Pattern
- **Pattern Description**: Conditional compilation of test main function based on preprocessor definitions. This pattern allows test files to be used in different build contexts while controlling when the test entry point is compiled.

- **Pattern Identification**:
  - `#ifdef TEST_MAIN_SYMBOL` or similar conditional compilation
  - `int main(void)` function definition within the conditional block
  - Test execution code within the main function
  - Preprocessor symbol typically defined by build system

- **Build Implications**: Build system controls test entry point compilation through preprocessor definitions. Test files can be included as modules or compiled as standalone executables. Test main compilation must be coordinated with build target configuration.

- **Source Example**:
```c
#ifdef TESTS_MAIN
int main(void) {
    /* test execution code */
    return 0;
}
#endif
```

- **Exceptional Cases**: None.

## Build Feature Control Patterns

### [SIP-BUILD-001] Feature Toggle Pattern
- **Pattern Description**: Conditional compilation blocks controlled by preprocessor definitions that enable or disable specific features during build time. This pattern allows different build configurations from the same source code.

- **Pattern Identification**:
  - `#ifdef FEATURE_NAME` conditional compilation blocks
  - Feature-specific code within conditional blocks
  - Feature names often indicate their purpose or testing mode
  - Features typically disabled by default, enabled by build configuration

- **Build Implications**: Build system can define feature flags to enable specific behaviors. Different build targets can enable different feature combinations. Feature flags provide build-time customization without runtime overhead.

- **Source Example**:
```c
#ifdef ENABLE_DEBUG_FEATURES
/* debug-specific code */
#endif

#ifdef FORCE_TEST_CONDITIONS
/* testing-specific behavior */
#endif
```

- **Exceptional Cases**: None.

## API Definition Patterns

### [SIP-API-001] Public Type Definition Pattern
- **Pattern Description**: Type definitions and enumerations in header files that establish the library's public API surface. This pattern defines the stable interface contract that consuming applications depend on.

- **Pattern Identification**:
  - `typedef struct` declarations for opaque or public types
  - `enum` definitions for public constants and error codes
  - Type names typically include library prefix
  - Forward declarations for opaque types

- **Build Implications**: These types define the ABI that must remain stable across library versions. Build system must ensure type definitions are available to all consuming projects. Changes to public types require careful consideration of backward compatibility.

- **Source Example**:
```c
typedef struct my_object_t MY_Object;
typedef struct my_array_t MY_Array;

enum my_result_t {
    MY_SUCCESS = 0,
    MY_ERROR = -1
};
typedef int MY_Status;
```

- **Exceptional Cases**: None.

### [SIP-API-002] Function Declaration Export Pattern
- **Pattern Description**: Complete set of public function declarations that define the library's callable interface and exported symbols. This pattern establishes the library's functional API and linking requirements.

- **Pattern Identification**:
  - Function declarations without implementation in header files
  - Consistent parameter naming and documentation patterns
  - Return types using public type definitions
  - Function names typically include library prefix

- **Build Implications**: These functions must be implemented in the library and available for linking. Build system must ensure all declared functions are compiled into the library target. Function signatures establish the ABI contract for consuming applications.

- **Source Example**:
```c
MY_Object * my_create_object(const char *name);
MY_Status my_process_data(MY_Object *obj, const void *data, size_t len);
void my_destroy_object(MY_Object *obj);
```

- **Exceptional Cases**: None.
