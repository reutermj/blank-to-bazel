# Conversion Plan - Parson - Make

## Project Level
- **Source Build System**: Make
- **Target Build System**: Bazel
- **Build Assessment Reference**: `build-assessment.parson.make.md`

## Conversion Items

### 1. C Compiler Configuration
- **Assessment Reference**: Build Step 1 - C Compiler Configuration (CC and CFLAGS variables)
- **Conversion Description**: Convert Make compiler variables to Bazel cc_library copts attribute
- **Bazel Implementation**:
  ```python
  cc_library(
      name = "parson",
      srcs = ["parson.c"],
      hdrs = ["parson.h"],
      copts = ["-O0", "-g", "-Wall", "-Wextra", "-std=c89", "-pedantic-errors"],
      visibility = ["//visibility:public"],
  )
  ```
- **Conversion Rationale**: Bazel cc_library copts attribute provides equivalent functionality to Make CFLAGS, allowing per-target compilation flags
- **Guideline Reference**: Compiler and Flag Configuration to Bazel Toolchain

### 2. C++ Compiler Configuration
- **Assessment Reference**: Build Step 2 - C++ Compiler Configuration (CPPC and CPPFLAGS variables)
- **Conversion Description**: Convert Make C++ compiler variables to Bazel cc_test copts for C++ compatibility testing
- **Bazel Implementation**:
  ```python
  cc_test(
      name = "testcpp",
      srcs = ["tests.c"],
      deps = [":parson"],
      copts = ["-O0", "-g", "-Wall", "-Wextra", "-DTESTS_MAIN"],
      linkstatic = True,
  )
  ```
- **Conversion Rationale**: Bazel cc_test handles C++ compilation automatically; copts provide equivalent flags to CPPFLAGS
- **Guideline Reference**: Cross-Language Compatibility to Bazel cc_test with C++

### 3. Default Target Declaration
- **Assessment Reference**: Build Step 3 - Default Target Declaration (all: test testcpp test_hash_collisions)
- **Conversion Description**: Convert Make default target to Bazel test_suite grouping multiple tests
- **Bazel Implementation**:
  ```python
  test_suite(
      name = "all_tests",
      tests = [
          ":test",
          ":testcpp",
          ":test_hash_collisions",
      ],
  )
  ```
- **Conversion Rationale**: Bazel test_suite provides equivalent functionality to Make's default target grouping
- **Guideline Reference**: Phony Target to Bazel Test Suite

### 4. Phony Target Declaration
- **Assessment Reference**: Build Step 4 - Phony Target Declaration (.PHONY: test testcpp test_hash_collisions)
- **Conversion Description**: Convert Make phony targets to Bazel test_suite
- **Bazel Implementation**:
  ```python
  test_suite(
      name = "all_tests",
      tests = [
          ":test",
          ":testcpp",
          ":test_hash_collisions",
      ],
  )
  ```
- **Conversion Rationale**: Bazel test_suite inherently handles the concept of phony targets for grouping tests
- **Guideline Reference**: Phony Target to Bazel Test Suite

### 5. C Test Target
- **Assessment Reference**: Build Step 5 - C Test Target (test: tests.c parson.c)
- **Conversion Description**: Convert Make test target to Bazel cc_test with library dependency
- **Bazel Implementation**:
  ```python
  cc_test(
      name = "test",
      srcs = ["tests.c"],
      deps = [":parson"],
      copts = ["-DTESTS_MAIN"],
  )
  ```
- **Conversion Rationale**: Bazel cc_test provides equivalent functionality with proper dependency management between test and library
- **Guideline Reference**: Target Rules to Bazel cc_binary/cc_test

### 6. C++ Test Target
- **Assessment Reference**: Build Step 6 - C++ Test Target (testcpp: tests.c parson.c)
- **Conversion Description**: Convert Make C++ test target to Bazel cc_test for C++ compatibility
- **Bazel Implementation**:
  ```python
  cc_test(
      name = "testcpp",
      srcs = ["tests.c"],
      deps = [":parson"],
      copts = ["-DTESTS_MAIN"],
      linkstatic = True,
  )
  ```
- **Conversion Rationale**: Bazel cc_test handles C++ compilation; linkstatic ensures static linking similar to Make behavior
- **Guideline Reference**: Cross-Language Compatibility to Bazel cc_test with C++

### 7. Hash Collision Test Target
- **Assessment Reference**: Build Step 7 - Hash Collision Test Target (test_hash_collisions with -DPARSON_FORCE_HASH_COLLISIONS)
- **Conversion Description**: Convert Make hash collision test target to Bazel cc_test with special define
- **Bazel Implementation**:
  ```python
  cc_test(
      name = "test_hash_collisions",
      srcs = ["tests.c"],
      deps = [":parson"],
      copts = ["-DTESTS_MAIN"],
      defines = ["PARSON_FORCE_HASH_COLLISIONS"],
  )
  ```
- **Conversion Rationale**: Bazel defines attribute provides equivalent functionality to Make's -D flag
- **Guideline Reference**: Multiple Build Variants to Bazel Config/Select

### 8. Clean Target
- **Assessment Reference**: Build Step 8 - Clean Target (clean: rm -f test *.o)
- **Conversion Description**: No explicit conversion needed - Bazel provides built-in clean functionality
- **Bazel Implementation**:
  ```bash
  # No BUILD file equivalent needed - use bazel clean
  bazel clean
  ```
- **Conversion Rationale**: Bazel's built-in clean command provides equivalent functionality to Make's clean target
- **Guideline Reference**: Clean Target to Bazel Clean

### 9. Public Interface - parson.h
- **Assessment Reference**: Public Interface Analysis - Public Headers (parson.h)
- **Conversion Description**: Convert implicit public header to explicit Bazel hdrs attribute
- **Bazel Implementation**:
  ```python
  cc_library(
      name = "parson",
      srcs = ["parson.c"],
      hdrs = ["parson.h"],
      visibility = ["//visibility:public"],
  )
  ```
- **Conversion Rationale**: Bazel hdrs attribute explicitly declares public headers; visibility makes library publicly accessible
- **Guideline Reference**: Implicit Public Interface to Bazel Visibility

### 10. Public Interface - parson.c
- **Assessment Reference**: Public Interface Analysis - Public Libraries (parson.c)
- **Conversion Description**: Convert implicit public library to explicit Bazel cc_library with public visibility
- **Bazel Implementation**:
  ```python
  cc_library(
      name = "parson",
      srcs = ["parson.c"],
      hdrs = ["parson.h"],
      visibility = ["//visibility:public"],
  )
  ```
- **Conversion Rationale**: Bazel cc_library with public visibility explicitly declares the library as public interface
- **Guideline Reference**: Implicit Public Interface to Bazel Visibility

### 11. Private Interface - tests.c
- **Assessment Reference**: Public Interface Analysis - Private Elements (tests.c)
- **Conversion Description**: Convert test source to Bazel cc_test srcs attribute
- **Bazel Implementation**:
  ```python
  cc_test(
      name = "test",
      srcs = ["tests.c"],
      deps = [":parson"],
      copts = ["-DTESTS_MAIN"],
  )
  ```
- **Conversion Rationale**: Bazel cc_test srcs attribute properly isolates test code from public library interface
- **Guideline Reference**: Target Rules to Bazel cc_binary/cc_test

## Complete Bazel BUILD File

```python
package(default_visibility = ["//visibility:public"])

cc_library(
    name = "parson",
    srcs = ["parson.c"],
    hdrs = ["parson.h"],
    copts = ["-O0", "-g", "-Wall", "-Wextra", "-std=c89", "-pedantic-errors"],
    visibility = ["//visibility:public"],
)

cc_test(
    name = "test",
    srcs = ["tests.c"],
    deps = [":parson"],
    copts = ["-DTESTS_MAIN"],
)

cc_test(
    name = "testcpp",
    srcs = ["tests.c"],
    deps = [":parson"],
    copts = ["-DTESTS_MAIN"],
    linkstatic = True,
)

cc_test(
    name = "test_hash_collisions",
    srcs = ["tests.c"],
    deps = [":parson"],
    copts = ["-DTESTS_MAIN"],
    defines = ["PARSON_FORCE_HASH_COLLISIONS"],
)

test_suite(
    name = "all_tests",
    tests = [
        ":test",
        ":testcpp",
        ":test_hash_collisions",
    ],
)
```

## Dependency Mapping

### Build Step Dependencies Preserved
- **Compiler Configuration → Library Target**: Make's CC/CFLAGS variables preserved in Bazel cc_library copts
- **Source Dependencies → Bazel deps**: Make's source file dependencies (tests.c depends on parson.c) converted to Bazel deps attribute
- **Cross-Language Dependencies**: Make's C++ compatibility testing preserved in Bazel cc_test

### Public/Private Interface Preservation
- **Public Interface**: parson.h and parson.c marked as public through hdrs attribute and public visibility
- **Private Interface**: tests.c kept private as cc_test srcs, not exposed in public interface
- **Dependency Isolation**: Clear separation between library (parson) and tests maintained in Bazel structure
