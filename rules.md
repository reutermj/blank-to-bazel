# Convert build to bazel build

## Instructions

1. Assess the existing build system first to understand its structure, dependencies, and build semantics.
2. Convert the build script in this repo to a Bazel build configuration that preserves the original build semantics.
3. **Do not modify source files** - the goal is to get the original sources building with Bazel without changing the underlying codebase.
4. **Do not modify the "Instructions" and "Detailing future improvements" sections** of the rules.md file when creating rules.new.md.
5. As part of the summary, write out a rules.new.md that includes a revision of this file with the considerations outlined in: Detailing future improvements.

## Key Principles

- Preserve original build behavior and output
- Focus on creating Bazel BUILD files and configuration without altering source code
- Base all improvements on actual conversion experience, not speculation
- Maintain the integrity of the instruction framework while enhancing the guidance sections

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

### Bazel Anti-Patterns

### Translation Dictionary

### Process Guidelines

### Original Build Analysis Guidelines
