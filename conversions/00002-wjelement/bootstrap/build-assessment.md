# Assess Existing Build System

This document provides instructions for analyzing existing build systems to understand what they accomplish and how they work. The goal is to create comprehensive assessments that document the build system's intent and mechanisms, while simultaneously building a knowledge base of interpretation patterns that can be applied to future build system analyses.

For every build script present:
1. Read the existing `build-interpretation-runbook.<build system>.md` to understand patterns and approaches for interpreting the build system.
2. Assess the existing build system first to understand its structure, dependencies, and build semantics.
3. Build Assessment Creation: create a `build-assessment.<project>.<build system>.md` file that provides a complete analysis of the current build system.
4. Interpretation Runbook Maintenance: update `build-interpretation-runbook.<build system>.md` files to preserve knowledge for future assessments.

## Build Assessment (`build-assessment.<project>.<build system>.md`)

The build assessment documents what the current build system accomplishes and how it works. This serves as a comprehensive record of the build system's intent, structure, and mechanisms that can be referenced during conversion or maintenance activities.

### Build Assessment Guidelines
* Follow the structure and sections defined in `build-interpretation-runbook.<build system>.md`
* Assess at the logical build step/target level, explaining each component part of the logical build step/target
* Focus on understanding what the build system is accomplishing and how it accomplishes it
* Maintain consistent formatting that mirrors the section structure in the interpretation runbook

### Build Assessment Contents
* For each assessment point, include:
  - **Reference Tag**: uniquely identify the build assessment point (e.g., `[BAP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>]`)
  - **Description**: explain what this specific build construct accomplishes and how it works
  - **Source Code Snippet**: relevant source code that demonstrates this assessment point.
  - **Path to Source File**: file path relative to the project root where this construct is located
  - **Line Number in Source File**: specific line number or range where this construct appears
  - **References**: Reference to all relevant interpretation pattern tags (e.g., `[IP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>]`, `[PROPOSED-IP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>]`)

#### Example

````
### [BAP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>] <TITLE>
- **Description**: <DESCRIPTION>

- **Source Code Snippet**:
```<LANGUAGE>
<SOURCE CODE EXAMPLE>
```

- **Path to Source File**: <RELATIVE FILE PATH>
- **Line Number in Source File**: <LINE NUMBER>
- **References**: [IP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>]
````

## Interpretation Runbook Maintenance (`build-interpretation-runbook.proposal.<build system>.md`)

The interpretation runbook maintenance captures new knowledge about how to understand and interpret build system constructs. This builds a reusable knowledge base that improves the quality and consistency of future build system assessments by documenting patterns for recognizing what different build constructs accomplish. If the existing runbook sufficiently covers the build constructs encountered during analysis, note that and do not feel obligated to propose new patterns.

### Interpretation Pattern Guidelines
* Only propose new interpretation patterns when the existing runbook does not adequately cover build constructs encountered during this project assessment
* Propose new interpretation patterns based on build constructs encountered during this project assessment
* Propose improvements, clarifications, and extensions to existing interpretation patterns where evidence from the current analysis reveals new ways build systems accomplish their goals
* Base all proposals on actual project experience, not theoretical scenarios and best practices
* Structure proposals into logically consistent sections that can be integrated into the main interpretation runbook
* Focus on a single, specific build system concept that accomplishes one logical purpose (e.g., setting a library's public header, configuring version information, or defining shared object versioning)
* Avoid combining multiple logically distinct build system concepts into a single pattern, even if they commonly appear together in the same build construct
* Use generic, project-agnostic source code examples that exemplify the pattern rather than including project-specific names, paths, or configuration details
* Ensure proposed interpretation patterns can be validated and applied consistently across different projects

### Interpretation Pattern Contents
* For each proposed interpretation pattern:
  - **Reference Tag**: uniquely identify the pattern (e.g., `[PROPOSED-IP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>]` for new, `[IP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>-REVISION]` for improvements)
  - **Pattern Description**: explain what this pattern means for the existing build system's behavior and intent
  - **Pattern Identification**: describe the specific code constructs, syntax, or structural elements that indicate this pattern is present in the build source code
  - **Source Example**: add project agnostic source code snippet that demonstrates the pattern
  - **Exceptional Cases**: list situations where the pattern identification criteria are met but additional build script context indicates a different interpretation should be applied. Format as a list where each exception includes:
    - **Exception Description**: explain what this exceptional case means for the build system's behavior and intent when the additional context is present, and why it differs from the base pattern interpretation
    - **Exception Pattern Identification**: pattern identification criteria for recognizing when this exception applies
    - **Exception Source Example**: source example showing the base pattern plus the exceptional information
    - **Exception Handler Reference**: reference tag for the pattern that handles this exceptional case (or "NONE" if no pattern exists yet)

#### Example 1

````
### [IP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>] <TITLE>
- **Pattern Description**: <DESCRIPTION>

- **Pattern Identification**: 
  - <ITEM 1>
  - <ITEM 1>
  - ...

- **Source Example**:
```<LANGUAGE>
<SOURCE CODE EXAMPLE>
```

- **Exceptional Cases**: None.
````

#### Example 2

````
### [IP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>] <TITLE>
- **Pattern Description**: <DESCRIPTION>

- **Pattern Identification**: 
  - <ITEM 1>
  - <ITEM 1>
  - ...

- **Source Example**:
```<LANGUAGE>
<SOURCE CODE EXAMPLE>
```

- **Exceptional Cases**: 
  - **Exception Description**: <DESCRIPTION>
  - **Exception Pattern Identification**:
    - <ITEM 1>
    - <ITEM 1>
    - ...
  - **Exception Source Example**:
```<LANGUAGE>
<SOURCE CODE EXAMPLE>
```
  - **Exception Handler Reference**: [IP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>]
````
