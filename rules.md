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
  module(name = "parson", version = "1.5.3")
  bazel_dep(name = "rules_cc", version = "0.1.2")
  ```
- **Context**: Bzlmod is the modern dependency management system that provides better version resolution, transitive dependency handling, and eliminates the need for manual repository rules.

#### Minimize Target Count
- **Pattern**: Create the minimal number of targets needed, avoiding redundant targets
- **Example**: Use a single `cc_library` for the main library and separate `cc_test` targets for different test configurations, rather than multiple overlapping targets
- **Context**: Reduces build complexity and maintenance overhead while keeping the build graph clean. Each test configuration should be a separate target when they have different compiler flags or defines.

#### Proper Load Statements
- **Pattern**: Always use explicit load statements for rule definitions
- **Example**: `load("@rules_cc//cc:defs.bzl", "cc_library", "cc_test")`
- **Context**: Modern Bazel requires explicit loading of rules from their repositories

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

#### Preserve Compiler Flags
- **Pattern**: Map important compiler flags from the original build system to `copts`
- **Example**: Transfer `-Wall`, `-std=c99`, `-pedantic`, `-O2` from Makefile to Bazel `copts`
- **Context**: Maintains the same build behavior and code quality standards

#### Use Standard Target Naming
- **Pattern**: Use descriptive, consistent target names that match the project structure
- **Example**: Main library as `"parson"`, test as `"test"`, variant tests as `"test_hash_collisions"`
- **Context**: Makes the build intuitive and follows Bazel conventions

#### Handle Conditional Compilation in Single-File Projects
- **Pattern**: When source files contain both library code and test code (conditional on preprocessor defines), create separate targets that compile the same source with different defines
- **Example**: 
  ```starlark
  cc_library(name = "lib", srcs = ["code.c"])
  cc_test(name = "test", srcs = ["code.c"], copts = ["-DTEST_MAIN"])
  ```
- **Context**: Common pattern in C projects where test code is embedded in the same file as library code

#### Use Correct rules_cc Version
- **Pattern**: Use the latest stable version of rules_cc that matches Bazel's recommendations
- **Example**: Use `bazel_dep(name = "rules_cc", version = "0.1.2")` instead of older versions
- **Context**: Ensures compatibility and access to latest features

#### Handle Test Data Files with glob()
- **Pattern**: Use `glob()` to include test data files that tests depend on
- **Example**: `data = glob(["tests/*.txt"])` for tests that read external files
- **Context**: Ensures test data is available during test execution and properly tracked as dependencies

#### Separate Library and Test Targets for Better Modularity
- **Pattern**: Create a separate `cc_library` target for the main code and reference it from tests when the library doesn't use conditional compilation
- **Example**: 
  ```starlark
  cc_library(name = "mylib", srcs = ["lib.c"], hdrs = ["lib.h"])
  cc_test(name = "test", srcs = ["test.c"], deps = [":mylib"])
  ```
- **Context**: This is the preferred approach for most projects as it improves build modularity, allows the library to be reused by other targets, and avoids code duplication. Only use source duplication when the library source requires different compilation for tests (e.g., conditional compilation with preprocessor defines).

#### Handle Conditional Compilation Test Patterns
- **Pattern**: When library source files use conditional compilation to include test code (via preprocessor defines), include the source files in both library and test targets with different defines
- **Example**: 
  ```starlark
  cc_library(name = "parson", srcs = ["parson.c"], hdrs = ["parson.h"])
  cc_test(name = "test", srcs = ["tests.c", "parson.c", "parson.h"], copts = ["-DTESTS_MAIN"])
  ```
- **Context**: Some C projects embed test main functions or test-specific code within the main source files, controlled by preprocessor defines. In these cases, the source must be compiled differently for tests vs. library use. For projects without conditional compilation, prefer using `deps = [":library"]` in tests instead of duplicating sources.

#### Document Omitted Non-Standard Practices
- **Pattern**: When the original build system contains non-standard practices that shouldn't be replicated, document the decision with comments in the BUILD file
- **Example**: Add comments explaining why a "testcpp" target that compiles C files with C++ was intentionally omitted
- **Context**: Provides transparency about conversion decisions and prevents confusion about missing targets

#### Use Appropriate Optimization and Debug Flags
- **Pattern**: Preserve debug and optimization flags from the original build for consistency during development and testing
- **Example**: Include `-O0 -g` flags in test targets when they were present in the original build
- **Context**: Maintains the same debugging experience and performance characteristics during development

### Harmful Patterns

#### Using WORKSPACE.bazel (Legacy System)
- **Anti-pattern**: Defining dependencies in WORKSPACE.bazel files
- **Why harmful**: WORKSPACE is legacy, has poor version resolution, and conflicts with Bzlmod
- **Alternative**: Use MODULE.bazel with `bazel_dep()` declarations
- **Example**: Instead of `git_repository()` in WORKSPACE, use `bazel_dep(name = "rules_cc", version = "0.1.2")` in MODULE.bazel

#### Redundant Targets
- **Anti-pattern**: Creating multiple targets that build the same or overlapping sets of files
- **Why harmful**: Increases build complexity, maintenance overhead, and potential for inconsistencies
- **Alternative**: Create minimal, well-defined targets with clear separation of concerns

#### Missing Load Statements
- **Anti-pattern**: Assuming rules like `cc_library` are globally available
- **Why harmful**: Causes build failures in modern Bazel versions
- **Alternative**: Always include proper `load()` statements at the top of BUILD files

#### Using hdrs in cc_test
- **Anti-pattern**: Separating headers and sources in `cc_test` targets using both `hdrs` and `srcs`
- **Why harmful**: `cc_test` doesn't support the `hdrs` attribute, causing build failures
- **Alternative**: Put all source files (including headers) in the `srcs` attribute

#### Hardcoded Tool Paths
- **Anti-pattern**: Using absolute paths to compilers or tools from the original build system
- **Why harmful**: Reduces portability and conflicts with Bazel's toolchain system
- **Alternative**: Let Bazel handle toolchain selection automatically

#### Ignoring Version Warnings
- **Anti-pattern**: Ignoring warnings about dependency version mismatches
- **Why harmful**: Can lead to build inconsistencies and compatibility issues
- **Alternative**: Update MODULE.bazel to use the resolved version shown in warnings

#### Converting Non-Standard Build Practices
- **Anti-pattern**: Forcing Bazel to replicate non-standard behavior from the original build system (e.g., compiling C files with C++ compiler)
- **Why harmful**: Introduces complexity, violates language conventions, and may not work reliably across different environments
- **Alternative**: Omit non-standard practices and document the decision with comments
- **Example**: Document why a "testcpp" target that compiles C files with g++ was intentionally omitted

#### Overcomplicating Simple Projects
- **Anti-pattern**: Creating complex target hierarchies for simple single-library projects
- **Why harmful**: Adds unnecessary complexity without benefits
- **Alternative**: Use simple, direct target structure matching the project's actual complexity

#### Over-Engineering Module Dependencies
- **Anti-pattern**: Creating complex dependency structures when a simple, self-contained build would suffice
- **Why harmful**: Adds unnecessary complexity for projects that are designed to be standalone
- **Alternative**: For simple libraries like single-file JSON parsers, prefer self-contained targets that include all necessary sources directly

#### Blindly Replicating All Original Targets
- **Anti-pattern**: Converting every target from the original build system without evaluating whether it adds value
- **Why harmful**: Can introduce complexity, non-standard practices, or unnecessary maintenance burden
- **Alternative**: Evaluate each target's purpose and convert only those that provide legitimate value; document omissions clearly

#### Unnecessary Source Code Duplication
- **Anti-pattern**: Including library source files in test targets when the library doesn't require different compilation for tests
- **Why harmful**: Creates code duplication, increases build time, and makes the build graph less modular
- **Alternative**: Use `deps = [":library"]` in test targets unless the library source uses conditional compilation that requires different preprocessor defines for tests
- **Example**: Instead of `cc_test(srcs = ["test.c", "lib.c"])`, use `cc_test(srcs = ["test.c"], deps = [":lib"])`

### Translation Dictionary

#### Build Targets
- **Make**: `target: dependencies` → **Bazel**: `cc_library(name = "target", deps = ["//path/to:dependencies"])`
- **Make**: `$(CC) -c file.c` → **Bazel**: `srcs = ["file.c"]` in appropriate rule
- **Make**: `$(AR) library.a objects` → **Bazel**: `cc_library()` automatically creates static library
- **Make**: `executable: source.c` → **Bazel**: `cc_binary()` or `cc_test()` depending on purpose
- **Make**: Multiple executables from same sources → **Bazel**: Multiple `cc_test()` targets with different `copts`
- **Make**: Test target with data files → **Bazel**: `cc_test()` with `data = glob(["path/*.ext"])`
- **Make**: Phony targets for testing → **Bazel**: `cc_test()` targets that run automatically

#### Compiler Flags
- **Make**: `CFLAGS = -Wall -O2` → **Bazel**: `copts = ["-Wall", "-O2"]`
- **Make**: `CPPFLAGS = -DMACRO` → **Bazel**: `defines = ["MACRO"]` or `copts = ["-DMACRO"]`
- **Make**: `LDFLAGS = -lm` → **Bazel**: `linkopts = ["-lm"]`
- **Make**: Conditional flags with `-DTEST_MAIN` → **Bazel**: `copts = ["-DTEST_MAIN"]` in test target
- **Make**: Debug flags `-g -O0` → **Bazel**: `copts = ["-g", "-O0"]`
- **Make**: Standard flags `-std=c89 -pedantic-errors` → **Bazel**: `copts = ["-std=c89", "-pedantic-errors"]`
- **Make**: Feature-specific flags `-DPARSON_FORCE_HASH_COLLISIONS` → **Bazel**: `copts = ["-DPARSON_FORCE_HASH_COLLISIONS"]` in specific test target

#### File Organization
- **Make**: Header files referenced by inclusion → **Bazel**: `hdrs = ["header.h"]` in `cc_library`
- **Make**: Source files in compilation → **Bazel**: `srcs = ["source.c"]`
- **Make**: Test executables → **Bazel**: `cc_test()`
- **Make**: Single file with embedded tests → **Bazel**: Separate `cc_library` and `cc_test` targets
- **Make**: Test data files → **Bazel**: `data = glob(["tests/*.txt"])`
- **Make**: Mixed source/header in test compilation → **Bazel**: All files in `srcs = ["file.c", "file.h"]` for cc_test
- **Make**: Self-contained test files including library source → **Bazel**: Include library sources directly in test `srcs`

#### Dependencies
- **Make**: Implicit header dependencies → **Bazel**: Explicit `deps = ["//path/to:library"]`
- **Make**: Library linking with `-l` → **Bazel**: `deps` to appropriate `cc_library` targets
- **Make**: Direct compilation of all sources → **Bazel**: Proper target dependencies
- **Make**: External dependencies → **Bazel**: `bazel_dep()` in MODULE.bazel
- **Make**: Test data file dependencies → **Bazel**: `data = glob(["pattern"])` attribute

#### Build System Concepts
- **Make**: Phony targets like `clean`, `test` → **Bazel**: Built-in commands like `bazel clean`, `bazel test`
- **Make**: Pattern rules → **Bazel**: Rule definitions (typically not needed for simple builds)
- **Make**: All-in-one executable build → **Bazel**: Separate library and test/binary targets
- **Make**: Variable assignments → **Bazel**: Target attributes and `copts`
- **Make**: Test execution in build → **Bazel**: `bazel test` command runs tests separately from build

#### Conditional Compilation
- **Make**: `ifdef` conditionals → **Bazel**: `select()` statements with config_setting
- **Make**: Platform-specific flags → **Bazel**: Platform-specific `copts` using `select()`
- **Make**: Preprocessor-based feature toggles → **Bazel**: Different targets with different `copts`
- **Make**: Test vs production builds → **Bazel**: Separate `cc_library` and `cc_test` targets
- **Make**: Macro-controlled main functions → **Bazel**: Test targets with appropriate preprocessor defines

#### Modern Dependency Management
- **Make**: External libraries via system packages → **Bazel**: `bazel_dep()` in MODULE.bazel
- **Make**: Git submodules → **Bazel**: Bzlmod dependencies or `git_repository()` rules
- **Make**: pkg-config integration → **Bazel**: Explicit dependency declarations

#### CMake Translations
- **CMake**: `add_library(name sources)` → **Bazel**: `cc_library(name = "name", srcs = ["sources"])`
- **CMake**: `target_include_directories()` → **Bazel**: `hdrs` attribute for public headers
- **CMake**: `target_link_libraries()` → **Bazel**: `deps` attribute
- **CMake**: `install()` commands → **Bazel**: Handled by packaging rules (not covered in simple conversions)
- **CMake**: `set_target_properties()` → **Bazel**: Appropriate target attributes
- **CMake**: Version management → **Bazel**: Module version in MODULE.bazel
- **CMake**: Public headers → **Bazel**: `hdrs` attribute with appropriate `visibility`

#### Meson Translations
- **Meson**: `library(name, sources)` → **Bazel**: `cc_library(name = "name", srcs = sources)`
- **Meson**: `include_directories()` → **Bazel**: `hdrs` attribute in `cc_library`
- **Meson**: `declare_dependency()` → **Bazel**: `cc_library` target that can be referenced in `deps`
- **Meson**: `install_headers()` → **Bazel**: Use `hdrs` with proper `visibility`
- **Meson**: Project version → **Bazel**: Module version in MODULE.bazel
- **Meson**: Default options for standards → **Bazel**: `copts` with appropriate flags

#### Multi-Build System Projects
- **Pattern**: Projects with Makefile, CMakeLists.txt, and meson.build → **Bazel**: Single BUILD.bazel that preserves the core functionality
- **Approach**: Analyze all build systems to understand the complete feature set, then create a Bazel build that captures the essential functionality
- **Example**: For a library with multiple build systems, focus on the library target and essential test configurations rather than replicating every variant

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
