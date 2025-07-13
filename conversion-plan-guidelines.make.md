# Conversion Plan Guidelines - Make

## Overview
Guidelines for converting Make build systems to Bazel, preserving build semantics and public interface characteristics.

## Guidelines

### Compiler and Flag Configuration
- **Name**: Compiler and Flag Configuration to Bazel Toolchain
- **Description**: Converts Make compiler variables (CC, CFLAGS, CPPC, CPPFLAGS) to Bazel cc_library attributes and toolchain configuration
- **Source Build System Example**:
  ```makefile
  CC = compiler_name
  CFLAGS = -flag1 -flag2 -DMACRO_NAME
  CPPC = cpp_compiler_name
  CPPFLAGS = -flag1 -flag2 -DMACRO_NAME
  ```
- **Bazel Example**:
  ```python
  cc_library(
      name = "library_name",
      srcs = ["source_file.c"],
      hdrs = ["include/header_file.h"],
      includes = ["include"],
      copts = ["-flag1", "-flag2"],
      defines = ["MACRO_NAME"],
      visibility = ["//visibility:public"],
  )
  ```
- **Applicability**: When Make defines CC, CXX, CFLAGS, CXXFLAGS, or similar compiler configuration variables
- **Projects Applied**: parson

### Target Rules to Bazel Targets
- **Name**: Make Target Rules to Bazel cc_binary/cc_test
- **Description**: Converts Make target rules with dependencies and build commands to appropriate Bazel targets
- **Source Build System Example**:
  ```makefile
  target_name: source_file.c dependency_file.c
  	gcc $(CC) $(CFLAGS) -o $@ source_file.c dependency_file.c
  	./$@
  ```
- **Bazel Example**:
  ```python
  cc_test(
      name = "target_name",
      srcs = ["source_file.c"],
      deps = [":dependency_target"],
  )
  ```
- **Applicability**: When Make defines executable targets with source dependencies and execution commands
- **Projects Applied**: parson

### Phony Target to Bazel Commands
- **Name**: Phony Target Declaration to Bazel Commands
- **Description**: Make phony targets that group multiple targets are build system conveniences that don't translate to Bazel BUILD files. Use Bazel command-line target patterns instead.
- **Source Build System Example**:
  ```makefile
  .PHONY: target1 target2 target3
  all: target1 target2 target3
  ```
- **Bazel Example**:
  ```bash
  # No BUILD file equivalent needed - use bazel command-line patterns
  bazel build :target1 :target2 :target3
  bazel test :target1 :target2 :target3
  bazel test //...  # for all targets in package
  ```
- **Applicability**: When Make uses .PHONY declarations and default targets that group multiple test or build targets
- **Projects Applied**: parson

### Multiple Build Variants to Bazel Config
- **Name**: Multiple Target Variants to Bazel Config/Select
- **Description**: Converts Make targets with different compilation flags or defines to Bazel targets using config_setting and select()
- **Source Build System Example**:
  ```makefile
  target_variant: source_file.c library_file.c
  	$(CC) $(CFLAGS) -DSPECIAL_DEFINE -o $@ source_file.c library_file.c
  ```
- **Bazel Example**:
  ```python
  cc_test(
      name = "target_variant",
      srcs = ["source_file.c"],
      deps = [":library_target"],
      defines = ["SPECIAL_DEFINE"],
  )
  ```
- **Applicability**: When Make has multiple similar targets with different compilation flags or preprocessor defines
- **Projects Applied**: parson

### Clean Target to Bazel Clean
- **Name**: Clean Target Implementation to Bazel Clean
- **Description**: Make clean targets are handled by Bazel's built-in clean command, no explicit conversion needed
- **Source Build System Example**:
  ```makefile
  clean:
  	rm -f executable_name *.o
  ```
- **Bazel Example**:
  ```bash
  # No BUILD file equivalent needed - use bazel clean
  bazel clean
  ```
- **Applicability**: When Make has clean targets for removing generated files
- **Projects Applied**: parson

### Implicit Public Interface to Bazel Visibility
- **Name**: Implicit Public Interface Through Source File Patterns to Bazel Visibility
- **Description**: Converts Make's implicit public interface identification through consistent source file usage patterns to explicit Bazel visibility rules
- **Source Build System Example**:
  ```makefile
  # Library sources used consistently across targets indicates public interface
  target1: test_source.c library_source.c
  target2: test_source.c library_source.c
  target3: test_source.c library_source.c
  ```
- **Bazel Example**:
  ```python
  cc_library(
      name = "library_target",
      srcs = ["library_source.c"],
      hdrs = ["include/library_header.h"],
      includes = ["include"],
      visibility = ["//visibility:public"],
  )
  ```
- **Applicability**: When Make shows consistent library source usage across multiple targets with clear separation from test sources
- **Projects Applied**: parson

### Root Directory Headers to Include Directory
- **Name**: Root Directory Public Headers to Include Directory Organization
- **Description**: Bazel cannot use the root directory (.) in the includes attribute. When Make projects have public headers in the root directory that need to be accessible via includes, the conversion plan should include moving the header from the root directory into an include/ folder and updating the Bazel cc_library to reference the new location.
- **Source Build System Example**: Not applicable - this is a structural conversion requirement
- **Bazel Example**: Not applicable - this is a structural conversion requirement
- **Applicability**: When Make projects have public headers in the root directory that need to be accessible via includes
- **Projects Applied**: parson
