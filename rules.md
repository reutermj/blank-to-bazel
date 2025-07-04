# Convert build to bazel build

## Instructions

1. Assess the existing build system first to understand its structure, dependencies, and build semantics.
2. Convert the build script in this repo to a Bazel build configuration that preserves the original build semantics.
3. **Do not modify source files** - the goal is to get the original sources building with Bazel without changing the underlying codebase.
4. As part of the summary, write out a rules.new.md that includes a revision of this file with the considerations outlined in: Detailing future improvements.
- **Do not modify the "Instructions" and "Detailing future improvements" sections** of the rules.md file when creating rules.new.md.
- Base all suggested improvements to rules.new.md on actual conversion experience, not speculation
- Preserve existing rules even if they're not used in the current translation
- **Avoid redundancy** - focus on adding rules to the section where they're most relevant and avoid duplicating content across multiple sections

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
    - Specific examples with context and explanation
    - Applicable situations: when this best practice should be used (based only on actual scenarios encountered during the current conversion)
    - Exceptions: cases where this best practice should be avoided or modified (based only on actual exceptions found during the current conversion)
    - Leave situation and exception sections blank rather than making assumptions or guesses
  * Update existing best practices with new discoveries that improve their flexibility and applicability across a wider range of cases.
  * Focus on practices that have been validated through actual conversion experience rather than theoretical guidelines.

2. Bazel Anti-Patterns:
  * Summarize all Bazel  anti-patterns encountered during the conversion.
  * For each  anti-patterns, document:
    - Specific examples explaining why the practice should be avoided
    - Applicable situations: contexts where this anti-pattern typically occurs (based only on actual instances found during the current conversion)
    - Exceptions: rare cases where this practice might be acceptable or unavoidable (based only on actual exceptions encountered during the current conversion)
    - Alternative approaches: recommended best practices to use instead
    - Leave situation and exception sections blank rather than making assumptions or guesses
  * Update existing  anti-patterns with new discoveries that expand their scope and improve recognition of potential pitfalls.
  * Focus on anti-patterns that have been observed in actual conversion work rather than theoretical concerns.

3. Translation Dictionary:
  * Create a comprehensive translation dictionary mapping source build concepts to Bazel equivalents.
  * Categorize translations by source build system (e.g., Make, CMake, Autotools).
  * For each translation, document:
    - Applicable situations: specific contexts where this translation applies (based only on actual occurrences in the current translation work)
    - Exceptions: cases where this translation should not be used (based only on actual exceptions encountered in the current translation work)
    - Leave situation and exception sections blank rather than making assumptions or guesses
  * Update existing dictionary entries with new discoveries that improve translation flexibility and clarity.
  * Focus on translations that have been validated through actual migration experience rather than theoretical mappings.

4. Process Guidelines:
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

5. Original Build Analysis Guidelines:
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
- **Example**: Replace `workspace(name = "sds")` in WORKSPACE file with `MODULE.bazel` containing:
  ```starlark
  module(
      name = "sds",
      version = "1.0.0",
  )
  
  bazel_dep(name = "rules_cc", version = "0.1.2")
  ```
- **Explanation**: bzlmod is the modern Bazel dependency management system that provides better version resolution and cleaner configuration compared to legacy WORKSPACE files
- **Applicable situations**: All new Bazel projects should use bzlmod for dependency management

#### Use latest versions of Bazel rules
- **Example**: Use `rules_cc` version `0.1.2` instead of older versions in MODULE.bazel
- **Explanation**: Latest rule versions include bug fixes, performance improvements, and new features that improve build reliability. latest versions of important deps are:
  * `rules_cc`: `0.1.2`
  * `platforms`: `1.0.0`
- **Applicable situations**: When setting up dependencies in MODULE.bazel

#### Produce well-scoped targets
- **Example**: Create both `cc_library` and `cc_test` targets:
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
      defines = ["TESTS_MAIN"],
  )
  ```
- **Explanation**: Well-scoped targets improve modularity, reusability, and build performance by allowing Bazel to build only what's necessary
- **Applicable situations**: When translating build systems with multiple outputs or components

#### Use dependencies between targets
- **Example**: Make test targets depend on library targets:
  ```starlark
  cc_test(
      name = "sds-test",
      srcs = ["sds-test.c"],
      deps = [":sds"],
  )
  ```
- **Explanation**: Using `deps` allows Bazel to manage dependencies correctly, ensuring proper build order and enabling incremental builds
- **Applicable situations**: When a target relies on code from another target
- **Exceptions**: Cannot be used when tests are embedded in source files using conditional compilation

#### Use `cc_test` for test targets instead of `cc_binary`
- **Example**: Use `cc_test(name = "test", ...)` not `cc_binary(name = "test", ...)`
- **Explanation**: `cc_test` integrates with Bazel's test infrastructure, enabling features like test result reporting, parallel execution, and proper test discovery
- **Applicable situations**: Any executable that runs tests should use `cc_test`

#### Preserve original compiler flags and defines
- **Example**: Maintain original flags like `-O0 -g -Wall -Wextra -std=c89 -pedantic-errors` in `copts`
- **Explanation**: Original compiler flags ensure the Bazel build produces equivalent behavior and catches the same issues as the original build system
- **Applicable situations**: When the original build system specifies particular compiler behavior

#### Handle embedded tests with conditional compilation properly
- **Example**: For `#ifdef TESTS_MAIN` in source files, replicate all source files in the test target with `defines = ["TESTS_MAIN"]`
- **Explanation**: When tests are conditionally compiled within source files, the test target must compile the source files directly with the test defines rather than depending on the library target, which breaks the normal dependency pattern but is necessary for this compilation model
- **Applicable situations**: When examining source files reveals conditional test compilation blocks

#### Explicitly load cc rules in BUILD files
- **Example**: Add `load("@rules_cc//cc:defs.bzl", "cc_library", "cc_binary", "cc_test")` at the top of BUILD files.
- **Explanation**: Modern rules_cc requires explicit loading of cc rules rather than relying on implicit availability, improving build clarity and compatibility
- **Applicable situations**: When using cc_library, cc_test, or other cc rules in BUILD files

### Bazel Anti-Patterns

#### Using legacy WORKSPACE when bzlmod is available
- **Example**: Creating new `workspace(name = "project")` files instead of using `MODULE.bazel`
- **Explanation**: Legacy WORKSPACE files are harder to maintain, have worse dependency resolution, and have been phased out in favor of bzlmod
- **Alternative approaches**: Always use bzlmod (MODULE.bazel) for new projects
- **Applicable situations**: Always

#### Using `cc_binary` for test executables
- **Example**: Defining `cc_binary(name = "test-name", ...)` for test programs
- **Explanation**: `cc_binary` targets don't integrate with Bazel's test framework, missing features like test reporting and proper test discovery
- **Alternative approaches**: Use `cc_test` to integrate properly with Bazel's test framework
- **Applicable situations**: When converting Makefile targets that produce test executables

#### Attempting library dependency for embedded conditional tests
- **Example**: Trying to make test target depend on library when tests are conditionally compiled within the same source files
- **Explanation**: When tests are embedded using conditional compilation, the library target is compiled without test defines, so the test target cannot use it and must compile the source files directly
- **Alternative approaches**: Replicate source files in test target with appropriate defines
- **Applicable situations**: When source code analysis reveals `#ifdef TEST_MAIN` or similar patterns

#### Translating cross-language compilation patterns
- **Example**: Creating `cc_test` targets that use C++ compiler on C source files like Makefile `testcpp` targets
- **Explanation**: Cross-language compilation is often a testing artifact rather than a genuine build requirement and adds unnecessary complexity to Bazel builds
- **Alternative approaches**: Focus on building proper language-specific targets and use Bazel's native multi-language support for genuine interoperability needs
- **Applicable situations**: When Makefile contains targets using different compilers on the same source files

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

### Process Guidelines

#### Initial workspace setup sequence
1. **Command**: Create MODULE.bazel with dependencies
   - **Explanation**: bzlmod requires MODULE.bazel file to define the module and its dependencies
   - **Applicable situations**: Setting up bzlmod-based workspace
   - **Expected output**: Successful module resolution on first build

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
   - **Explanation**: Runs all test targets to verify they execute correctly
   - **Applicable situations**: Running all test targets
   - **Expected output**: Test pass/fail summary with both bazel and the original test script reporting all passing tests
   - **Timing**: After build verification succeeds

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
