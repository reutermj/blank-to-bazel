# Convert build to bazel build

## Instructions

1. Assess the existing build system first to understand its structure, dependencies, and build semantics. Before moving onto the next step, create a summary of all build steps including:
- Step name/type
- Purpose
- Inputs
- Outputs
- Key Parameters
- Dependencies
- How the step typically translates to bazel build steps
2. Convert the build script in this repo to a Bazel build configuration that preserves the original build semantics.
3. **Do not modify source files unless instructed** - the goal is to get the original sources building with Bazel while avoiding changes to the underlying codebase.
4. As part of the summary, write out a rules.new.md that includes a revision of this file with the considerations outlined in: Detailing future improvements.
- **Do not modify the "Instructions" and "Detailing future improvements" sections** of the rules.md file when creating rules.new.md.
- Base all suggested improvements to rules.new.md on actual conversion experience, not speculation
- Preserve existing rules even if they're not used in the current translation
- **Avoid redundancy** - focus on adding rules to the section where they're most relevant and avoid duplicating content across multiple sections
5. **5. Produce a detailed conversion log** - Create a comprehensive log file called `conversion-log.md` documenting the conversion process specific to this project. This log should capture project-specific challenges, solutions, and decisions made during the conversion to aid future conversions of updated versions of the same project.
- Document every build configuration translation with exact source-to-target mappings:
  * **Original Source**: Complete original build system configuration (CMake, Make, etc.)
  * **Bazel Translation**: Corresponding Bazel BUILD rule(s)
  * **Mapping Rationale**: Why this specific translation approach was chosen
  * **Discovery Process**: How you identified what the original build system was doing
- For every source file that required modification:
  * **Original Source Code**: Exact original code block that needed changes
  * **Output Source Code**: Exact output code block
  * **Modification Rationale**: Why the change was necessary for Bazel compatibility
  * **Discovery Process**: Step-by-step process of how you discovered this change was needed (what failed, what errors occurred, how you diagnosed the issue)
  * **Failure Symptoms**: Specific error messages or behaviors that led to identifying the need for this change
- For every file that was moved, renamed, or restructured:
  * **File Movement Mapping**: Original location → New location
  * **Movement Rationale**: Why the move was necessary for Bazel
  * **Discovery Process**: How you identified the need for the move (what constraint or requirement drove this)
  * **Impact Analysis**: What other files or configurations were affected by this move
- Document every interaction during the conversion process (except the initial prompt with this rules file):
  * **Human Prompt**: Exact prompt text given to the agent
  * **Prompt Purpose**: Why this specific prompt was necessary at this point in the conversion
  * **Agent Response Summary**: Key outcomes, actions taken, or solutions provided by the agent
  * **Success/Failure**: Whether the prompt achieved its intended goal
  * **Follow-up Required**: What additional prompts or iterations were needed
6. **Update module index** - Add an entry to `module-index.md` documenting the converted module for future reference. The entry should include:
   - The `bazel_dep` declaration needed to depend on this converted module
   - A list of the main public targets that other conversions would typically want to depend on

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

#### Modify test exit codes for framework compatibility when necessary
- **Explanation**: Some test frameworks return failure count as exit code, but Bazel expects tests to return 0 for success. When migrating such tests, modify the main function to return 0 when the expected number of test failures occur.
- **Example**: Modify cmocka test exit codes for "_fail" tests:
  ```c
  int main(void) {
      const struct CMUnitTest tests[] = {
          cmocka_unit_test(test_assert_true_fail),
      };
      int result = cmocka_run_group_tests(tests, NULL, NULL);
      int expected_failures = 1; // Number of tests expected to fail
      return (result == expected_failures) ? 0 : 1;
  }
  ```
- **Applicable situations**: When converting cmocka-based tests or other frameworks that return failure counts as exit codes
- **What to avoid**: Leaving test exit codes unchanged when they conflict with Bazel's test success expectations, causing false test failures

#### Add missing includes for C89 compatibility with modern libraries
- **Explanation**: When using modern C libraries with C89 code, some standard headers may not be automatically included. Adding the necessary includes ensures compatibility without changing the underlying C standard or API usage.
- **Example**: Add stdint.h for CMocka compatibility:
  ```c
  #include <setjmp.h>
  #include <stdarg.h>
  #include <stddef.h>
  #include <stdint.h>    // Add for intmax_t, uintmax_t definitions
  #include <stdlib.h>
  
  #include <cmocka.h>
  ```
- **Applicable situations**: When C89 code uses libraries that depend on types defined in headers not automatically included in C89
- **What to avoid**: Changing the entire codebase to a newer C standard when only a simple include is needed

#### Use genrule for custom tool-based file generation
- **Explanation**: When the original build system uses custom tools to generate source files or lookup tables, genrule provides a clean way to model this dependency in Bazel. The `tools` attribute ensures the tool is built before execution.
- **Example**: Generate lookup tables using a custom tool:
  ```starlark
  cc_binary(
      name = "generator_tool",
      srcs = ["tool/generator.c"],
  )
  
  genrule(
      name = "gen_lookup_table",
      outs = ["tab/lookup.inc"],
      cmd = "$(location :generator_tool) --output $@",
      tools = [":generator_tool"],
  )
  
  cc_library(
      name = "library",
      srcs = [
          "src/lib.c",
          ":gen_lookup_table",
      ],
      includes = ["tab"],  # Allow relative includes to find generated files
  )
  ```
- **Applicable situations**: When original build systems have custom code generation steps or table pre-computation

#### Adjust includes path for generated file access
- **Explanation**: When source files use relative includes to access generated files, adding the generated file's directory to includes allows the relative path to resolve correctly in Bazel's sandbox without modifying source code.
- **Example**: Add "tab" to includes for generated table files:
  ```starlark
  cc_library(
      name = "library",
      srcs = [
          "src/lib.c",           # Contains #include "../tab/lookup.inc"
          ":gen_lookup_table",   # Generates tab/lookup.inc
      ],
      includes = ["include", "tab"],  # "tab" allows ../tab/ to resolve from src/
  )
  ```
- **Applicable situations**: When source files use relative includes (like `../tab/file.inc`) to access generated files and avoiding source modification is preferred

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

##### Generated file dependencies with custom tools
- **Make pattern**: 
  ```makefile
  tool: tool_sources
  	$(CC) -o tool tool_sources
  
  generated.inc: tool
  	./tool --output generated.inc
  
  target: source.c generated.inc
  	$(CC) -o target source.c
  ```
- **Bazel equivalent**:
  ```starlark
  cc_binary(
      name = "tool",
      srcs = ["tool_sources"],
  )
  
  genrule(
      name = "gen_file",
      outs = ["generated.inc"],
      cmd = "$(location :tool) --output $@",
      tools = [":tool"],
  )
  
  cc_binary(
      name = "target",
      srcs = ["source.c", ":gen_file"],
  )
  ```
- **Explanation**: Makefile tool-based generation translates to cc_binary for the tool plus genrule for the generation step
- **Applicable situations**: When Makefiles use custom tools to generate source files during build

##### Static library creation from object files
- **Make pattern**:
  ```makefile
  lib.a: obj1.o obj2.o obj3.o
  	ar qc lib.a obj1.o obj2.o obj3.o
  	ranlib lib.a
  ```
- **Bazel equivalent**:
  ```starlark
  cc_library(
      name = "lib",
      srcs = ["src1.c", "src2.c", "src3.c"],
      hdrs = ["public.h"],
  )
  ```
- **Explanation**: Makefile static library creation with ar/ranlib maps to cc_library in Bazel
- **Applicable situations**: When Makefile creates static libraries from compiled object files

##### Include path specification
- **Make pattern**: `$(CC) -Iinclude_dir -o target source.c`
- **Bazel equivalent**:
  ```starlark
  cc_binary(
      name = "target",
      srcs = ["source.c"],
      includes = ["include_dir"],
  )
  ```
- **Explanation**: Makefile `-I` flags for include directories map to the `includes` attribute in Bazel
- **Applicable situations**: When Makefile specifies additional include search paths

##### Complex compiler flag aggregation
- **Make pattern**: 
  ```makefile
  EXTRA_CFLAGS ?= -Werror -Wall -Wextra -funsigned-char -fwrapv -Wconversion
  TESTCFLAGS ?= -std=c99 -Wno-error=deprecated-declarations -O0
  target: $(CC) $(EXTRA_CFLAGS) $(TESTCFLAGS) source.c
  ```
- **Bazel equivalent**:
  ```starlark
  cc_test(
      name = "target",
      srcs = ["source.c"],
      copts = [
          "-Werror", "-Wall", "-Wextra", "-funsigned-char", "-fwrapv", 
          "-Wconversion", "-std=c99", "-Wno-error=deprecated-declarations", "-O0"
      ],
  )
  ```
- **Explanation**: Makefile variable aggregation of compiler flags maps to a single flattened copts list in Bazel
- **Applicable situations**: When Makefiles use multiple variables to build up complex compiler flag sets

##### Library linking
- **Make pattern**: `$(CC) -o target source.c test_utils.c -lcmocka`
- **Bazel equivalent**:
  ```starlark
  cc_test(
      name = "target",
      srcs = ["source.c"],
      deps = [":test_utils", "@cmocka//:cmocka"],
  )
  ```
- **Explanation**: External library linking (-l flags) maps to external dependencies in deps, while local object files become local cc_library dependencies
- **Applicable situations**: When Makefile links multiple source files and external libraries together

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

##### CMake test loop pattern
- **CMake pattern**: 
  ```cmake
  foreach(_TEST ${TESTS})
      add_cmocka_test(${_TEST} SOURCES ${_TEST}.c)
  endforeach()
  ```
- **Bazel equivalent**:
  ```starlark
  [cc_test(
      name = test_name,
      srcs = [test_name + ".c"],
      deps = [":library"],
  ) for test_name in ALL_TESTS]
  ```
- **Explanation**: CMake loops over test lists map to Bazel list comprehensions for efficient target generation
- **Applicable situations**: When CMake generates multiple similar test targets from a list

##### CMake compiler flags preservation
- **CMake pattern**: `target_compile_options(target PRIVATE ${DEFAULT_C_COMPILE_FLAGS} -DHAVE_CONFIG_H)`
- **Bazel equivalent**: `copts = ["-std=c99", "-DHAVE_CONFIG_H", "-D_GNU_SOURCE"]`
- **Explanation**: CMake compile options transfer directly to Bazel copts attribute
- **Applicable situations**: When CMake sets specific compiler behavior that must be preserved

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

#### Test exit code diagnosis and resolution
- **Command**: `bazel test //... --test_summary=detailed`
- **Explanation**: Provides detailed test results showing exit codes for failed tests, enabling identification of exit code mismatches
- **Applicable situations**: When tests fail with non-zero exit codes but are expected to pass
- **Expected output**: Test summary showing "Exit 1", "Exit 3" etc. for tests that should pass
- **Troubleshooting steps**: Examine test framework exit code semantics and modify test main functions if framework returns failure counts instead of 0/1

#### Incremental test validation workflow
- **Command sequence**: Modify small batches of tests, then verify with `bazel test <batch>`
- **Explanation**: When applying systematic fixes to many tests, validate in small batches to isolate issues quickly
- **Applicable situations**: When modifying many test files with similar patterns (e.g., exit code changes)
- **Expected output**: Gradual progression from failing tests to passing tests
- **Timing**: During systematic test modifications to catch issues early

#### Verbose compiler output for include path debugging
- **Command**: `bazel build --copt="-v" target_name`
- **Explanation**: Shows detailed compiler invocation including all include search paths, which is crucial for debugging relative include issues with generated files
- **Applicable situations**: When source files cannot find included files, especially generated files or files with relative includes
- **Expected output**: Compiler verbose output showing:
  ```
  #include "..." search starts here:
  .
  bazel-out/k8-fastbuild/bin
  #include <...> search starts here:
  include
  /usr/include
  ```
- **Troubleshooting steps**: Compare the include search paths with the expected location of missing files to determine if includes path adjustment is needed

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

#### CMake configuration complexity assessment
- **Method**: Examine CMake configuration files like `ConfigureChecks.cmake` and `config.h.cmake`
- **Explanation**: Complex CMake configuration detection can often be simplified with static configuration files for specific target platforms
- **Applicable situations**: When CMake projects use extensive feature detection and configuration generation
- **Expected findings**: Large numbers of feature checks that may be simplified for target platform
- **Key indicators**: Files with `check_function_exists`, `check_include_file`, complex `#cmakedefine` templates

#### Source inclusion anti-pattern detection
- **Method**: Use `grep_search` to find `#include "../src/*.c"` patterns in test files
- **Explanation**: Tests including source files directly indicate need for private library pattern to access internal functions
- **Applicable situations**: When tests need access to internal functions not exposed in public API
- **Expected findings**: Test files including implementation files for internal access
- **Key indicators**: `#include` directives pointing to source files rather than headers, comments about needing internal functions

#### Test framework exit code analysis
- **Method**: Run tests and examine exit codes to understand test framework semantics
- **Explanation**: Different test frameworks have different exit code conventions that may conflict with Bazel's expectations
- **Applicable situations**: When initial test runs show systematic failures with specific exit codes
- **Expected findings**: Test frameworks that return failure counts rather than 0/1 exit codes
- **Key indicators**: Tests failing with exit codes that match number of test failures, "_fail" suffix tests that should pass

#### Makefile dependency chain identification
- **Method**: Trace Makefile target dependencies to understand build ordering
- **Explanation**: Following the dependency chain reveals which targets must be built before others and helps identify code generation steps
- **Applicable situations**: When converting Makefiles with complex dependencies or generated files
- **Expected findings**: Clear understanding of build phases, such as tool building → file generation → library compilation → test linking
- **Key indicators**: Look for targets that depend on tools (like `generated.inc: tool`), custom file extensions (`.inc`, `.gen`), and multi-step compilation

#### Live build observation for generation patterns
- **Method**: Run `make clean && make` and observe the build sequence and output
- **Explanation**: Watching the actual build process reveals the true execution order and any build-time code generation
- **Applicable situations**: When static analysis of build files doesn't reveal the complete build process
- **Expected findings**: Actual compiler commands, tool execution order, and generated file creation
- **Key indicators**: Custom tools being built and executed, temporary files being created, multi-stage compilation
