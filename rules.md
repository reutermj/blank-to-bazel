# Convert build to bazel build

## Instructions

1. Assess the existing build system first to understand its structure, dependencies, and build semantics.
2. Convert the build script in this repo to a Bazel build configuration that preserves the original build semantics.
3. **Minimize source file modifications** - the goal is to get the original sources building with Bazel while avoiding changes to the underlying codebase. Only modify source files when absolutely necessary to get the build and tests to pass in the Bazel environment. If modifications are necessary, make them as minimal as possible.
4. As part of the summary, write out a rules.new.md that includes a revision of this file with the considerations outlined in: Detailing future improvements.
- **Do not modify the "Instructions" and "Detailing future improvements" sections** of the rules.md file when creating rules.new.md.
- Base all suggested improvements to rules.new.md on actual conversion experience, not speculation
- Preserve existing rules even if they're not used in the current translation
- **Avoid redundancy** - focus on adding rules to the section where they're most relevant and avoid duplicating content across multiple sections
5. **Produce a detailed conversion log** - Create a comprehensive log file called bazel-conversion.md documenting the conversion process specific to this project. This log should capture project-specific challenges, solutions, and decisions made during the conversion to aid future conversions of updated versions of the same project. Include details such as:
   - Project-specific build patterns encountered
   - Dependency resolution challenges and solutions
   - Source modifications made and why they were necessary
   - Build target organization decisions
   - Test configuration adaptations
   - Any project-specific workarounds or customizations
6. **Update module index** - Add an entry to `module-index.md` documenting the converted module for future reference. The entry should include:
   - The `bazel_dep` declaration needed to depend on this converted module
   - A list of the main public targets that other conversions would typically want to depend on
   - Brief descriptions of what each public target provides
   - Any notable configuration or usage considerations specific to this module

## Detailing future improvements

When writing your summary, focus on improving the rules.md for future translations based on concrete lessons learned from this specific build conversion. These improvements should be:
- Grounded in actual experience from the current translation work
- General enough to apply to other build script translations
- Evidence-based rather than speculative

Preserve the existing style and structure of rules.md while enhancing sections that can be made more flexible, comprehensive, or clear through actual discoveries from this conversion.

**Critical principle**: Only document situations, exceptions, and patterns that were actually encountered during this conversion. It is better to leave sections incomplete than to speculate or make assumptions about scenarios that did not occur.

**Output format**: Create a new file called `rules.new.md` as part of your summary that incorporates all improvements and discoveries from this conversion. This should be a complete, updated version of the rules that can be used for future translations.

Include in your summary the following:
1. Bazel Best Practices:
  * Summarize all Bazel best practices discovered during the conversion process.
  * For each best practice, document:
    - **Explanation**: A clear description of the practice and why it's beneficial
    - **Example**: Specific implementation showing the best practice in action with context
    - **Applicable situations**: When this best practice should be used (based only on actual scenarios encountered during the current conversion)
    - **What to avoid**: Explanation of practices that violate this best practice, with specific examples of what not to do
    - **Additional things to avoid**: If multiple distinct anti-patterns relate to this best practice, document each separately with explanations and examples
    - **Exceptions**: Cases where this best practice should be avoided or modified (based only on actual exceptions found during the current conversion)
    - **Additional things to avoid**: If multiple distinct exceptions to this best practice, document each separately with explanations and examples
  * Leave situation and exception sections blank rather than making assumptions or guesses
  * Update existing best practices with new discoveries that improve their flexibility and applicability across a wider range of cases.
  * Focus on practices that have been validated through actual conversion experience rather than theoretical guidelines.

2. Translation Dictionary:
  * Create a comprehensive translation dictionary mapping source build concepts to Bazel equivalents.
  * Categorize translations by source build system (e.g., Make, CMake, Autotools).
  * For each translation, document:
    - Applicable situations: specific contexts where this translation applies (based only on actual occurrences in the current translation work)
    - Exceptions: cases where this translation should not be used (based only on actual exceptions encountered in the current translation work)
    - Leave situation and exception sections blank rather than making assumptions or guesses
  * Update existing dictionary entries with new discoveries that improve translation flexibility and clarity.
  * Focus on translations that have been validated through actual migration experience rather than theoretical mappings.

3. Process Guidelines:
  * Document all Bazel commands, workflows, and diagnostic techniques discovered during the conversion process.
  * For each command or process step, document:
    - Specific command syntax and flags used
    - Applicable situations: when this command should be run (based only on actual scenarios encountered during the current conversion)
    - Expected output: what the command output looks like and how to interpret it (based only on actual command runs during the current conversion)
    - Exceptions: cases where this command should be avoided or modified (based only on actual exceptions found during the current conversion)
    - Troubleshooting steps: how to resolve common issues encountered with this command
    - Leave situation and exception sections blank rather than making assumptions or guesses
  * Update existing process guidelines with new discoveries that improve conversion efficiency and accuracy.
  * Focus on workflows that have been validated through actual conversion experience rather than theoretical processes.
  * Include timing recommendations: when in the conversion process each command is most effective.
  * Document command combinations and sequences that proved particularly useful during the conversion.

4. Original Build Analysis Guidelines:
  * Document all techniques, tools, and approaches discovered for understanding the source build system during the conversion process.
  * For each analysis technique, document:
    - Specific commands, tools, or methods used to analyze the original build
    - Applicable situations: when this technique should be used (based only on actual scenarios encountered during the current conversion)
    - Expected findings: what information this technique reveals and how to interpret it (based only on actual analysis performed during the current conversion)
    - Exceptions: cases where this technique is not effective or should be avoided (based only on actual exceptions found during the current conversion)
    - Key indicators: what to look for in build files, output, or documentation that signals important translation considerations
    - Leave situation and exception sections blank rather than making assumptions or guesses
  * Update existing analysis guidelines with new discoveries that improve understanding of source build systems.
  * Focus on analysis methods that have been validated through actual conversion experience rather than theoretical approaches.
  * Include patterns and anti-patterns found in original build systems that indicate translation complexity or potential issues.
  * Document source build system quirks and behaviors that directly impact the Bazel translation strategy.

## Conversion Guidelines

### Bazel Best Practices

#### Use bzlmod over legacy WORKSPACE
- **Explanation**: bzlmod is the modern Bazel dependency management system that provides better version resolution, cleaner configuration, and improved maintainability compared to legacy WORKSPACE files. It eliminates many of the complexities and conflicts that arise with traditional WORKSPACE-based dependency management.
- **Example**: Create `MODULE.bazel` containing the module and dependencies definition:
  ```starlark
  module(
      name = "name",
      version = "1.0.0",
  )
  
  bazel_dep(name = "rules_cc", version = "0.1.2")
  ```
- **Applicable situations**: All new Bazel projects should use bzlmod for dependency management
- **What to avoid**: Creating new `workspace(name = "project")` files instead of using `MODULE.bazel`. Legacy WORKSPACE files are harder to maintain, have worse dependency resolution, and have been phased out in favor of bzlmod. For example, avoid:
  ```starlark
  workspace(name = "project")
  
  load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")
  http_archive(
      name = "rules_cc",
      urls = ["https://github.com/bazelbuild/rules_cc/archive/refs/tags/0.1.2.tar.gz"],
      strip_prefix = "rules_cc-0.1.2",
  )
  ```

#### Use latest versions of Bazel rules
- **Example**: Use `rules_cc` version `0.1.2` instead of older versions in MODULE.bazel
- **Explanation**: Latest rule versions include bug fixes, performance improvements, and new features that improve build reliability. latest versions of important deps are:
  * `rules_cc`: `0.1.2`
  * `platforms`: `1.0.0`
- **Applicable situations**: When setting up dependencies in MODULE.bazel
- **What to avoid**: Using outdated versions of Bazel rules that may contain bugs, security issues, or lack important features. For example, avoid:
  ```starlark
  bazel_dep(name = "rules_cc", version = "0.0.9")    # Outdated version
  bazel_dep(name = "platforms", version = "0.0.8")   # Outdated version
  ```

#### Use dependencies between targets
- **Explanation**: Using `deps` allows Bazel to manage dependencies correctly, ensuring proper build order and enabling incremental builds. This creates a clean separation between library code and test code, making builds more efficient and maintainable.
- **Example**: Make test targets depend on library targets:
  ```starlark
  cc_library(
      name = "library",
      srcs = ["lib.c"],
      hdrs = ["lib.h"],
      visibility = ["//visibility:public"],
  )
  cc_test(
      name = "test",
      srcs = ["tests.c"],
      deps = [":library"],
  )
  ```
- **Applicable situations**: When a target relies on code from another target
- **What to avoid**: Replicating library sources and headers in test targets when proper dependency management is possible. This leads to unnecessary compilation overhead and maintenance burden. For example, avoid:
  ```starlark
  cc_library(
      name = "library",
      srcs = ["lib.c"],
      hdrs = ["lib.h"],
  )
  cc_test(
      name = "test",
      srcs = [
          "tests.c",
          "lib.c",  # Avoid duplicating library sources
      ],
      hdrs = ["lib.h"],  # Avoid duplicating library headers
  )
  ```
- **Exceptions**: Cannot be used when tests are embedded in source files using conditional compilation. When tests are conditionally compiled within the same source files (like `#ifdef TEST_MAIN`), the library target is compiled without test defines, so the test target cannot use it and must compile the source files directly with appropriate defines.
  ```starlark
  cc_library(
      name = "library",
      srcs = ["lib.c"],
      hdrs = ["lib.h"],
  )
  cc_test(
      name = "test",
      srcs = ["lib.c", "lib.h"],  # Replicate source and header files
      defines = ["TESTS_MAIN"],
  )
  ```

#### Use `cc_test` for test targets instead of `cc_binary`
- **Explanation**: `cc_test` integrates with Bazel's test infrastructure, enabling features like test result reporting, parallel execution, proper test discovery, and standardized test execution. This provides better visibility into test results and allows Bazel to manage test execution more effectively.
- **Example**: Use `cc_test(name = "test", ...)` not `cc_binary(name = "test", ...)`:
  ```starlark
  cc_test(
      name = "unit_test",
      srcs = ["test.c"],
      deps = [":library"],
  )
  ```
- **Applicable situations**: Any executable that runs tests should use `cc_test`
- **What to avoid**: Using `cc_binary` for test executables. `cc_binary` targets don't integrate with Bazel's test framework, missing features like test reporting and proper test discovery. For example, avoid:
  ```starlark
  cc_binary(
      name = "test-name",
      srcs = ["test.c"],
      deps = [":library"],
  )
  ```

#### Preserve original compiler flags and defines
- **Explanation**: Original compiler flags ensure the Bazel build produces equivalent behavior and catches the same issues as the original build system. This maintains build consistency and helps prevent regressions when migrating from other build systems.
- **Example**: Maintain original flags like `-O0 -g -Wall -Wextra -std=c89 -pedantic-errors` in `copts`:
  ```starlark
  cc_library(
      name = "library",
      srcs = ["lib.c"],
      hdrs = ["lib.h"],
      copts = ["-O0", "-g", "-Wall", "-Wextra", "-std=c89", "-pedantic-errors"],
  )
  ```
- **Applicable situations**: When the original build system specifies particular compiler behavior

#### Explicitly load cc rules in BUILD files
- **Explanation**: Modern rules_cc requires explicit loading of cc rules rather than relying on implicit availability. This improves build clarity, makes dependencies explicit, and ensures compatibility with current Bazel versions.
- **Example**: Add `load("@rules_cc//cc:defs.bzl", "cc_library", "cc_binary", "cc_test")` at the top of BUILD files:
  ```starlark
  load("@rules_cc//cc:defs.bzl", "cc_library", "cc_binary", "cc_test")
  
  cc_library(
      name = "library",
      srcs = ["lib.c"],
      hdrs = ["lib.h"],
  )
  ```
- **Applicable situations**: When using cc_library, cc_test, or other cc rules in BUILD files
- **What to avoid**: Using cc rules without explicit load
  ```starlark
  # avoid using cc_library without the load at the top of the file
  
  cc_library(
      name = "library",
      srcs = ["lib.c"],
      hdrs = ["lib.h"],
  )
  ```

#### Include data files for tests that read external files
- **Explanation**: Test targets need explicit data dependencies to access files during test execution in Bazel's sandbox environment. Without these dependencies, tests will fail because they cannot find the required files at runtime.
- **Example**: Add `data = glob(["tests/*.txt"])` to test targets that read test data files:
  ```starlark
  cc_test(
      name = "test",
      srcs = ["test.c"],
      data = glob(["tests/*.txt"]),
      deps = [":library"],
  )
  ```
- **Applicable situations**: When tests read configuration files, test data, or other external files during execution

#### Modify source files for Bazel sandbox compatibility only when necessary
- **Example**: Modify file paths in test code to use `$TEST_TMPDIR` for writable files:
  ```c
  const char* get_file_path(const char *filename) {
      static char path_buf[2048] = { 0 };
      const char *test_tmpdir;
      
      memset(path_buf, 0, sizeof(path_buf));
      
      /* Use TEST_TMPDIR for writable files when running under Bazel */
      test_tmpdir = getenv("TEST_TMPDIR");
      if (test_tmpdir && (strstr(filename, "test_2_serialized") != NULL)) {
          sprintf(path_buf, "%s/%s", test_tmpdir, filename);
      } else {
          sprintf(path_buf, "%s/%s", g_tests_path, filename);
      }
      return path_buf;
  }
  ```
- **Explanation**: Bazel's test sandbox prevents writing to data directories, so tests that create temporary files must use `$TEST_TMPDIR` to maintain functionality while following Bazel security practices
- **Applicable situations**: When tests fail due to file I/O permission errors in Bazel sandbox and functionality preservation requires minimal source modification

#### Separate public and private headers correctly
- **Explanation**: Bazel distinguishes between headers that are part of a library's public API (exposed to dependents) and headers that are only needed for internal compilation. This distinction enables better dependency management and prevents accidental exposure of internal implementation details.
- **Example**: Use `srcs` for private headers and `hdrs` for public headers:
  ```starlark
  cc_library(
      name = "library",
      srcs = [
          "lib.c",
          "internal.h",  # Private header, not exposed to dependents
      ],
      hdrs = [
          "public_api.h",  # Public header, exposed to dependents
      ],
  )
  ```
- **Applicable situations**: When converting build systems that install only specific headers as part of the public API
- **What to avoid**: Placing all headers in `hdrs` when some should be private. This exposes internal implementation details unnecessarily. For example, avoid:
  ```starlark
  cc_library(
      name = "library", 
      hdrs = [
          "public_api.h",   # Should be in hdrs
          "internal.h",     # Should be in srcs
      ],
  )
  ```

#### Handle source inclusion anti-patterns with private libraries
- **Explanation**: When tests include source files directly (e.g., `#include "../src/lib.c"`) to access internal functions, create a private library that exposes the source file as a header. This maintains proper dependency separation while allowing access to internals.
- **Example**: Create private library for internal access:
  ```starlark
  cc_library(
      name = "lib_private",
      hdrs = [
          "src/lib.c",           # Source file exposed as header
          "include/private.h",   # Private headers
      ],
      includes = ["src", "include"],
      visibility = ["//tests:__pkg__"],
  )
  
  cc_test(
      name = "internal_test",
      srcs = ["internal_test.c"],
      deps = [
          ":lib",         # Public library
          ":lib_private", # Private access
      ],
  )
  ```
- **Applicable situations**: When tests need access to internal functions not exposed in public API and originally include source files directly
- **What to avoid**: Duplicating source files in test targets or exposing internal functions in public API unnecessarily

### Translation Dictionary

#### Make to Bazel Translations

##### Simple C compilation with flags
- **Make pattern**: `$(CC) -o target source.c -Wall -std=c99 -pedantic -O2`
- **Bazel equivalent**: 
  ```starlark
  cc_binary(
      name = "target",
      srcs = ["source.c"],
      copts = ["-Wall", "-std=c99", "-pedantic", "-O2"],
  )
  ```
- **Explanation**: Makefile compiler flags map directly to the `copts` attribute in Bazel cc_* rules
- **Applicable situations**: Direct compilation commands in Makefiles

##### Test compilation with defines
- **Make pattern**: `$(CC) -o test tests.c -DTESTS_MAIN`
- **Bazel equivalent**:
  ```starlark
  cc_test(
      name = "test",
      srcs = ["tests.c"],
      defines = ["TESTS_MAIN"],
  )
  ```
- **Explanation**: Makefile `-D` flags for preprocessor defines map to the `defines` attribute in Bazel
- **Applicable situations**: When Makefile uses defines to enable test code

##### Multiple source file compilation
- **Make pattern**: `$(CC) -o target file1.c file2.c -DFLAG`
- **Bazel equivalent**:
  ```starlark
  cc_test(
      name = "target",
      srcs = ["file1.c", "file2.c"],
      defines = ["FLAG"],
  )
  ```
- **Explanation**: Multiple source files in Makefile commands map to multiple entries in the `srcs` list
- **Applicable situations**: When Makefile compiles multiple source files together

##### Conditional compilation with additional defines
- **Make pattern**: `$(CC) -o test_special tests.c parson.c -DTESTS_MAIN -DSPECIAL_FLAG`
- **Bazel equivalent**:
  ```starlark
  cc_test(
      name = "test_special", 
      srcs = ["tests.c", "parson.c"],
      defines = ["TESTS_MAIN", "SPECIAL_FLAG"],
  )
  ```
- **Explanation**: Multiple defines in Makefile map to multiple entries in the `defines` list
- **Applicable situations**: When Makefile uses multiple preprocessor defines for different test variants

##### Header dependencies
- **Make pattern**: `target: source.c header1.h header2.h`
- **Bazel equivalent**: Include headers in `srcs` for local headers or `hdrs` for public headers
- **Explanation**: Explicit header dependencies in Makefiles become implicit through Bazel's automatic dependency discovery, but headers still need to be listed in the appropriate attribute
- **Applicable situations**: When Makefile explicitly lists header dependencies

##### Clean targets
- **Make pattern**: `clean: rm -f target`
- **Bazel equivalent**: `bazel clean` command (no BUILD file equivalent needed)
- **Explanation**: Makefile clean targets don't need translation as Bazel provides its own clean command that manages all build outputs
- **Applicable situations**: Makefile clean rules don't need direct translation

##### Header inclusion in test targets
- **Make pattern**: Implicit header dependencies in test compilation
- **Bazel equivalent**: Explicitly list headers in `srcs` for test targets
- **Explanation**: When tests reference project headers, include all referenced headers in the test target's srcs to ensure proper compilation
- **Applicable situations**: When test targets need access to implementation headers

##### Test data file dependencies
- **Make pattern**: Tests implicitly access files from current directory
- **Bazel equivalent**: Add `data = glob(["tests/*.txt"])` to test targets
- **Explanation**: Bazel's sandbox isolates tests from the filesystem, so data files must be explicitly declared as dependencies
- **Applicable situations**: When tests read external files during execution

#### CMake to Bazel Translations

##### CMake library definition
- **CMake pattern**: `add_library(name source.c)`
- **Bazel equivalent**:
  ```starlark
  cc_library(
      name = "name",
      srcs = ["source.c"],
  )
  ```
- **Explanation**: CMake library targets map directly to Bazel cc_library rules
- **Applicable situations**: When CMake defines library targets

##### CMake public header installation
- **CMake pattern**: `install(FILES header.h DESTINATION include)`
- **Bazel equivalent**: Include in `hdrs` attribute
- **Explanation**: CMake install rules indicate which headers are part of public API
- **Applicable situations**: When determining which headers should be public vs private in Bazel

##### CMake compiler definitions
- **CMake pattern**: `target_compile_definitions(target PRIVATE DEFINE_NAME)`
- **Bazel equivalent**: `defines = ["DEFINE_NAME"]` in target
- **Explanation**: CMake compile definitions map to Bazel defines attribute
- **Applicable situations**: When CMake sets preprocessor definitions

##### CMake test target with cmocka
- **CMake pattern**: `add_cmocka_test(test_name SOURCES test.c)`
- **Bazel equivalent**:
  ```starlark
  cc_test(
      name = "test_name",
      srcs = ["test.c"],
      deps = [":cmocka_library"],
  )
  ```
- **Explanation**: CMake test utilities map to standard Bazel cc_test with appropriate dependencies
- **Applicable situations**: When CMake uses framework-specific test helper functions

### Process Guidelines

#### Initial workspace setup sequence
1. **Command**: Create MODULE.bazel with dependencies
   - **Explanation**: bzlmod requires MODULE.bazel file to define the module and its dependencies
   - **Applicable situations**: Setting up bzlmod-based workspace
   - **Expected output**: Successful module resolution on first build

2. **Command**: Update .gitignore with `bazel-*`
   - **Explanation**: Add `bazel-*` entry to .gitignore to exclude Bazel's generated symlink directories from version control
   - **Applicable situations**: Converting projects with existing .gitignore files to include Bazel artifacts
   - **Expected output**: .gitignore file contains `bazel-*` entry to exclude `bazel-bin`, `bazel-out`, `bazel-testlogs`, and `bazel-<workspace-name>` directories

#### Build verification workflow
1. **Command**: `bazel query //...`
   - **Explanation**: Verifies that Bazel can discover all targets in BUILD files before attempting compilation
   - **Applicable situations**: After creating initial BUILD files to verify basic configuration
   - **Expected output**: List of all targets found, or error messages for BUILD file syntax issues
   - **Timing**: Before running any build commands

2. **Command**: `bazel build //...`
   - **Explanation**: Builds all targets in the workspace to verify BUILD file correctness
   - **Applicable situations**: Verifying all targets build successfully
   - **Expected output**: "Build completed successfully" message with target count
   - **Timing**: After target discovery verification succeeds

3. **Command**: `bazel test --test_output=all //...`
   - **Explanation**: Runs all test targets to verify they execute correctly and provides complete test output for analysis
   - **Applicable situations**: Running all test targets to verify conversion success
   - **Expected output**: Complete test output with pass/fail details for all tests
   - **Timing**: After build verification succeeds
   - **Additional benefit**: Using `--test_output=all` eliminates need to run tests multiple times to see results

#### Troubleshooting zero targets found
- **Command**: `bazel query //...`
- **Explanation**: When build commands report "Found 0 targets", use query to verify target discovery
- **Applicable situations**: When `bazel build //...` reports finding zero targets unexpectedly
- **Expected output**: List of targets or BUILD file syntax errors
- **Troubleshooting steps**: Check BUILD file syntax, verify file locations match target definitions

#### Troubleshooting missing files
- **Command**: `bazel build :target --verbose_failures`
- **Explanation**: Provides detailed error messages when builds fail due to missing dependencies
- **Applicable situations**: When build fails with missing input files
- **Expected output**: Detailed error messages showing missing file paths
- **Troubleshooting steps**: Check srcs list in BUILD file against actual filesystem

#### Troubleshooting test sandbox file I/O failures
- **Command sequence**: Compare test failure patterns between original build and Bazel test output
- **Explanation**: When tests fail with file I/O errors but pass in original build system, the issue is likely Bazel sandbox preventing writes to read-only locations
- **Applicable situations**: When Bazel tests show fewer passing tests than original build due to file writing failures
- **Expected output**: Error messages indicating permission denied for file writes in test directories
- **Troubleshooting steps**: Modify source code to use `$TEST_TMPDIR` environment variable for writable file operations

#### Verification of behavior equivalence
- **Command sequence**: `make clean && make target && ./target` followed by `bazel test :target --test_output=all`
- **Explanation**: Compare original build system output with Bazel test output to verify identical behavior
- **Applicable situations**: Verifying that Bazel build produces equivalent results to original build system
- **Expected output**: Identical test output between original and Bazel builds
- **Timing**: Final verification step after successful Bazel build and test

### Original Build Analysis Guidelines

#### Makefile examination techniques
- **Method**: Read complete Makefile content to understand the build logic
- **Explanation**: Simple Makefiles contain all build logic in a single file that can be read entirely to understand the build process
- **Applicable situations**: Understanding simple build systems with single Makefile
- **Expected findings**: Target definitions, compiler flags, source dependencies
- **Key indicators**: Look for `$(CC)` patterns, `-D` define flags, explicit dependencies

#### Multi-build-system project analysis
- **Method**: Identify and prioritize among multiple build system files (Makefile, CMakeLists.txt, meson.build, package.json)
- **Explanation**: Projects may include multiple build system definitions for different platforms or package managers, requiring selection of the primary build system to translate
- **Applicable situations**: When projects contain multiple build definition files
- **Expected findings**: Different build systems with varying target definitions and complexity levels

#### Source code analysis for embedded tests
- **Method**: Search for conditional compilation blocks using `grep -n "#ifdef.*TEST" *.c *.h`
- **Explanation**: When Makefiles show test targets but no separate test files exist, tests are likely embedded in source files using conditional compilation
- **Applicable situations**: When Makefile shows test targets but no separate test files
- **Expected findings**: Conditional compilation blocks that enable test code
- **Key indicators**: `#ifdef TESTS_MAIN`, `#ifdef TEST`, or similar patterns in source files

#### README analysis for project structure understanding
- **Method**: Read README.md sections on building and usage
- **Explanation**: README files often contain critical information about intended usage patterns that affect how targets should be structured
- **Applicable situations**: Understanding intended usage patterns and library embedding
- **Expected findings**: Build instructions, dependency information, usage examples
- **Key indicators**: Embedding instructions, library usage patterns, build requirements

#### Build output comparison methodology
- **Method**: Execute original build and capture output, then compare with Bazel output
- **Explanation**: Direct comparison of build outputs ensures that the Bazel translation preserves original behavior exactly
- **Applicable situations**: Verifying successful translation of build semantics
- **Expected findings**: Identical test results, equivalent compilation behavior
- **Key indicators**: Same test pass/fail counts, identical test output messages, equivalent executable behavior

#### Cross-language compilation pattern detection
- **Method**: Analyze Makefile targets that use different compilers on the same source files
- **Explanation**: Some build systems include targets that compile C source with C++ compilers for compatibility testing, which typically should not be translated to Bazel
- **Applicable situations**: When Makefile contains targets with names like `testcpp` that use `$(CPPC)` or `g++` on C source files
- **Expected findings**: Testing artifacts rather than genuine build requirements
- **Key indicators**: Targets using different compiler variables on identical source files, compiler flags without language-specific requirements

#### CMake install analysis for API boundary detection
- **Method**: Examine CMake `install()` commands to identify public vs private headers
- **Explanation**: CMake install rules definitively show which headers are intended as public API
- **Applicable situations**: When converting CMake-based projects with complex header hierarchies
- **Expected findings**: Clear distinction between public and private headers
- **Key indicators**: Headers listed in `install(FILES ... DESTINATION include)` are public API

