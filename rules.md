# Convert build to bazel build

## Instructions

1. Assess the existing build system first.
2. Convert the build script in this repo to a Bazel build configuration preserving build semantics.
3. As part of the summary, write out a rules.new.md that includes a revision of of this file with the considerations outlined in: Detailing future improvements.

## Conversion Guidelines

### Helpful Patterns

#### Use Bzlmod (MODULE.bazel) instead of WORKSPACE
- **Pattern**: Define dependencies using `bazel_dep()` in MODULE.bazel
- **Example**: 
  ```starlark
  module(name = "sds", version = "1.0.0")
  bazel_dep(name = "rules_cc", version = "0.1.2")
  ```
- **Context**: Bzlmod is the modern dependency management system that provides better version resolution, transitive dependency handling, and eliminates the need for manual repository rules.
- **Category**: Modern Bazel Practices

#### Minimize Target Count
- **Pattern**: Create the minimal number of targets needed, avoiding redundant targets
- **Example**: Use a single `cc_library` for the main library and one `cc_test` for tests, rather than multiple overlapping targets
- **Context**: Reduces build complexity and maintenance overhead while keeping the build graph clean
- **Category**: Build Optimization

#### Proper Load Statements
- **Pattern**: Always use explicit load statements for rule definitions
- **Example**: `load("@rules_cc//cc:defs.bzl", "cc_library", "cc_test")`
- **Context**: Modern Bazel requires explicit loading of rules from their repositories
- **Category**: Modern Bazel Practices

#### Use srcs for All Sources in Tests
- **Pattern**: Put both source files and headers in `srcs` attribute for `cc_test` targets
- **Example**: 
  ```starlark
  cc_test(
      name = "test",
      srcs = ["src.c", "header.h", "test_helper.h"]
  )
  ```
- **Context**: `cc_test` doesn't support `hdrs` attribute; all files should go in `srcs`
- **Category**: Bazel Rule Usage

#### Preserve Compiler Flags
- **Pattern**: Map important compiler flags from the original build system to `copts`
- **Example**: Transfer `-Wall`, `-std=c99`, `-pedantic`, `-O2` from Makefile to Bazel `copts`
- **Context**: Maintains the same build behavior and code quality standards
- **Category**: Build Equivalence

#### Use Standard Target Naming
- **Pattern**: Use descriptive, consistent target names that match the project structure
- **Example**: Main library as `"sds"`, test as `"sds_test"`
- **Context**: Makes the build intuitive and follows Bazel conventions
- **Category**: Best Practices

#### Handle Conditional Compilation in Single-File Projects
- **Pattern**: When source files contain both library code and test code (conditional on preprocessor defines), create separate targets that compile the same source with different defines
- **Example**: 
  ```starlark
  cc_library(name = "lib", srcs = ["code.c"])
  cc_test(name = "test", srcs = ["code.c"], copts = ["-DTEST_MAIN"])
  ```
- **Context**: Common pattern in C projects where test code is embedded in the same file as library code
- **Category**: Source Organization

#### Use Correct rules_cc Version
- **Pattern**: Use the latest stable version of rules_cc that matches Bazel's recommendations
- **Example**: Use `bazel_dep(name = "rules_cc", version = "0.1.2")` instead of older versions
- **Context**: Ensures compatibility and access to latest features
- **Category**: Dependency Management

### Harmful Patterns

#### Using WORKSPACE.bazel (Legacy System)
- **Anti-pattern**: Defining dependencies in WORKSPACE.bazel files
- **Why harmful**: WORKSPACE is legacy, has poor version resolution, and conflicts with Bzlmod
- **Alternative**: Use MODULE.bazel with `bazel_dep()` declarations
- **Example**: Instead of `git_repository()` in WORKSPACE, use `bazel_dep(name = "rules_cc", version = "0.1.2")` in MODULE.bazel
- **Category**: Deprecated Practices

#### Redundant Targets
- **Anti-pattern**: Creating multiple targets that build the same or overlapping sets of files
- **Why harmful**: Increases build complexity, maintenance overhead, and potential for inconsistencies
- **Alternative**: Create minimal, well-defined targets with clear separation of concerns
- **Category**: Build Complexity

#### Missing Load Statements
- **Anti-pattern**: Assuming rules like `cc_library` are globally available
- **Why harmful**: Causes build failures in modern Bazel versions
- **Alternative**: Always include proper `load()` statements at the top of BUILD files
- **Category**: Build Errors

#### Using hdrs in cc_test
- **Anti-pattern**: Separating headers and sources in `cc_test` targets using both `hdrs` and `srcs`
- **Why harmful**: `cc_test` doesn't support the `hdrs` attribute, causing build failures
- **Alternative**: Put all source files (including headers) in the `srcs` attribute
- **Category**: Rule Misuse

#### Hardcoded Tool Paths
- **Anti-pattern**: Using absolute paths to compilers or tools from the original build system
- **Why harmful**: Reduces portability and conflicts with Bazel's toolchain system
- **Alternative**: Let Bazel handle toolchain selection automatically
- **Category**: Portability Issues

#### Ignoring Version Warnings
- **Anti-pattern**: Ignoring warnings about dependency version mismatches
- **Why harmful**: Can lead to build inconsistencies and compatibility issues
- **Alternative**: Update MODULE.bazel to use the resolved version shown in warnings
- **Category**: Dependency Management

### Translation Dictionary

#### Build Targets
- **Make**: `target: dependencies` → **Bazel**: `cc_library(name = "target", deps = ["//path/to:dependencies"])`
- **Make**: `$(CC) -c file.c` → **Bazel**: `srcs = ["file.c"]` in appropriate rule
- **Make**: `$(AR) library.a objects` → **Bazel**: `cc_library()` automatically creates static library
- **Make**: `executable: source.c` → **Bazel**: `cc_binary()` or `cc_test()` depending on purpose

#### Compiler Flags
- **Make**: `CFLAGS = -Wall -O2` → **Bazel**: `copts = ["-Wall", "-O2"]`
- **Make**: `CPPFLAGS = -DMACRO` → **Bazel**: `defines = ["MACRO"]` or `copts = ["-DMACRO"]`
- **Make**: `LDFLAGS = -lm` → **Bazel**: `linkopts = ["-lm"]`
- **Make**: Conditional flags with `-DTEST_MAIN` → **Bazel**: `copts = ["-DTEST_MAIN"]` in test target

#### File Organization
- **Make**: Header files referenced by inclusion → **Bazel**: `hdrs = ["header.h"]` in `cc_library`
- **Make**: Source files in compilation → **Bazel**: `srcs = ["source.c"]`
- **Make**: Test executables → **Bazel**: `cc_test()`
- **Make**: Single file with embedded tests → **Bazel**: Separate `cc_library` and `cc_test` targets

#### Dependencies
- **Make**: Implicit header dependencies → **Bazel**: Explicit `deps = ["//path/to:library"]`
- **Make**: Library linking with `-l` → **Bazel**: `deps` to appropriate `cc_library` targets
- **Make**: Direct compilation of all sources → **Bazel**: Proper target dependencies

#### Build System Concepts
- **Make**: Phony targets like `clean`, `test` → **Bazel**: Built-in commands like `bazel clean`, `bazel test`
- **Make**: Pattern rules → **Bazel**: Rule definitions (typically not needed for simple builds)
- **Make**: All-in-one executable build → **Bazel**: Separate library and test/binary targets

#### Conditional Compilation
- **Make**: `ifdef` conditionals → **Bazel**: `select()` statements with config_setting
- **Make**: Platform-specific flags → **Bazel**: Platform-specific `copts` using `select()`
- **Make**: Preprocessor-based feature toggles → **Bazel**: Different targets with different `copts`

#### Modern Dependency Management
- **Make**: External libraries via system packages → **Bazel**: `bazel_dep()` in MODULE.bazel
- **Make**: Git submodules → **Bazel**: Bzlmod dependencies or `git_repository()` rules
- **Make**: pkg-config integration → **Bazel**: Explicit dependency declarations

## Detailing future improvements

When writing your summary, think about improving the rules.md given to you for future translations. 
These improvements should be lessons learned from translating this build but be general enough to handle translating other build scripts.
Try to also preserve as much of the style of the existing rules.md, but do feel free to improve and extend existing sections if they can be made more flexible, cover more scenarios, or provide more clear examples.
Include in your summary these following:

1. Helpful Patterns:
  * Summarize all helpful patterns discovered during the conversion process.
  * Provide specific examples for each helpful pattern, including context and explanation.
  * Update existing helpful patterns to be more flexible and handle a wider range of cases.

2. Harmful Patterns:
  * Summarize all harmful patterns or anti-patterns encountered during the conversion.
  * Provide specific examples of each harmful pattern, explaining why they should be avoided.
  * Update existing harmful patterns to cover more scenarios and potential pitfalls.
  * Suggest alternatives or best practices for each harmful pattern.

3. Translation Dictionary:
  * Create a comprehensive translation dictionary mapping source build concepts to Bazel equivalents.
  * Categorize the translation dictionary by source build system.
  * Include common make/CMake functions, variables, and directives with their Bazel counterparts.
  * Provide context-specific translations where applicable (e.g., how certain patterns translate differently in different situations).
  * Update the existing translation dictionary with any new discoveries or more flexible translations.
