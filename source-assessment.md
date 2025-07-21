# Assess Source Code for Build Patterns

This document provides instructions for analyzing source code to identify build-relevant patterns and correlate findings with existing build system assessments. The goal is to create comprehensive source assessments that document build-relevant patterns in the codebase, correlate these patterns with build system findings, and simultaneously build a knowledge base of source code interpretation patterns that can be applied to future source assessments.

**Important**: This is not a functional assessment of the source code. The agent should not be attempting to understand what is the purpose of the project or how the project accomplishes its purpose. It is only to assess the build-relevant patterns in the source code.

For every source code directory/file structure present:
1. Read the existing `source-interpretation-runbook.md` to understand patterns and approaches for interpreting build-relevant source code patterns.
2. Read the corresponding `build-assessment.<project>.<build system>.md` to understand the build system's structure and identify areas for correlation.
3. Assess the source code for build-relevant patterns.
4. Source Assessment Creation: create a `source-assessment.<project>.md` file that provides a complete analysis of build-relevant patterns in the source code.
5. Interpretation Runbook Maintenance: update `source-interpretation-runbook.md` files to preserve knowledge for future assessments.

## Source Assessment (`source-assessment.<project>.md`)

The source assessment documents build-relevant patterns found in the source code and correlates them with findings from the build system assessment. This serves as a comprehensive record of how the source code structure and patterns relate to build requirements, dependencies, and configurations that can be referenced during build system conversion or maintenance activities.

**Important**: This assessment focuses exclusively on build-relevant patterns and correlations. Do not assess the functional purpose of the project, business logic, or how the project accomplishes its intended functionality.

### Source Assessment Guidelines
* Follow the structure and sections defined in `source-interpretation-runbook.md`
* Focus exclusively on build-relevant patterns - do not assess functional logic or business requirements
* Correlate findings with specific build assessment points from `build-assessment.<project>.<build system>.md`
* Identify source code patterns that indicate build dependencies, compilation requirements, linking needs, and configuration expectations
* Maintain consistent formatting that mirrors the section structure in the interpretation runbook

### Source Assessment Contents
* For each assessment point, include:
  - **Reference Tag**: uniquely identify the source assessment point (e.g., `[SAP-<CATEGORY>-<NUMBER>]`)
  - **Description**: explain what this specific source code pattern indicates for build requirements and how it relates to build system needs
  - **Source Code Snippet**: relevant source code that demonstrates this assessment point
  - **Path to Source File**: file path relative to the project root where this pattern is located
  - **Line Number in Source File**: specific line number or range where this pattern appears
  - **Build Correlation**: reference to related build assessment points and explain the relationship (e.g., `[BAP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>]`), or note "NOVEL FINDING" if no corresponding build assessment point exists
  - **References**: Reference to all relevant source interpretation pattern tags (e.g., `[SIP-<CATEGORY>-<NUMBER>]`, `[PROPOSED-SIP-<CATEGORY>-<NUMBER>]`)

#### Example

````
### [SAP-<CATEGORY>-<NUMBER>] <TITLE>
- **Description**: <DESCRIPTION>

- **Source Code Snippet**:
```<LANGUAGE>
<SOURCE CODE EXAMPLE>
```

- **Path to Source File**: <RELATIVE FILE PATH>
- **Line Number in Source File**: <LINE NUMBER>
- **Build Correlation**: [BAP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>] - <CORRELATION DESCRIPTION>
- **References**: [SIP-<CATEGORY>-<NUMBER>]
````

## Interpretation Runbook Maintenance (`source-interpretation-runbook.proposal.md`)

The source interpretation runbook maintenance captures new knowledge about how to recognize and interpret build-relevant source code patterns. This builds a reusable knowledge base that improves the quality and consistency of future source code assessments by documenting patterns for identifying how source code structure and patterns relate to build system requirements. If the existing runbook sufficiently covers the source patterns encountered during analysis, note that and do not feel obligated to propose new patterns.

### Source Interpretation Pattern Guidelines
* Only propose new interpretation patterns when the existing runbook does not adequately cover source code patterns encountered during this project assessment
* Propose new interpretation patterns based on build-relevant source code patterns encountered during this project assessment
* Propose improvements, clarifications, and extensions to existing interpretation patterns where evidence from the current analysis reveals new ways source code indicates build requirements
* Base all proposals on actual project experience, not theoretical scenarios and best practices
* Structure proposals into logically consistent sections that can be integrated into the main interpretation runbook
* Focus on a single, specific source code pattern that indicates one logical build requirement (e.g., indicating external library dependency, specifying compilation feature requirement, or defining public API surface)
* Avoid combining multiple logically distinct source patterns into a single interpretation pattern, even if they commonly appear together in the same source file
* Use generic, project-agnostic source code examples that exemplify the pattern rather than including project-specific names, paths, or implementation details
* Ensure proposed interpretation patterns can be validated and applied consistently across different projects

### Source Interpretation Pattern Contents
* For each proposed interpretation pattern:
  - **Reference Tag**: uniquely identify the pattern (e.g., `[PROPOSED-SIP-<CATEGORY>-<NUMBER>]` for new, `[SIP-<CATEGORY>-<NUMBER>-REVISION]` for improvements)
  - **Pattern Description**: explain what this source code pattern indicates for build system requirements and dependencies
  - **Pattern Identification**: describe the specific code constructs, syntax, or structural elements that indicate this pattern is present in the source code
  - **Build Implications**: explain what build system configuration or dependencies this pattern requires
  - **Source Example**: add project agnostic source code snippet that demonstrates the pattern
  - **Exceptional Cases**: list situations where the pattern identification criteria are met but additional source code context indicates a different build interpretation should be applied. Format as a list where each exception includes:
    - **Exception Description**: explain what this exceptional case means for build requirements when the additional context is present, and why it differs from the base pattern interpretation
    - **Exception Pattern Identification**: pattern identification criteria for recognizing when this exception applies
    - **Exception Source Example**: source example showing the base pattern plus the exceptional information
    - **Exception Handler Reference**: reference tag for the pattern that handles this exceptional case (or "NONE" if no pattern exists yet)

#### Example 1

````
### [SIP-<CATEGORY>-<NUMBER>] <TITLE>
- **Pattern Description**: <DESCRIPTION>

- **Pattern Identification**: 
  - <ITEM 1>
  - <ITEM 2>
  - ...

- **Build Implications**: <BUILD REQUIREMENTS DESCRIPTION>

- **Source Example**:
```<LANGUAGE>
<SOURCE CODE EXAMPLE>
```

- **Exceptional Cases**: None.
````

#### Example 2

````
### [SIP-<CATEGORY>-<NUMBER>] <TITLE>
- **Pattern Description**: <DESCRIPTION>

- **Pattern Identification**: 
  - <ITEM 1>
  - <ITEM 2>
  - ...

- **Build Implications**: <BUILD REQUIREMENTS DESCRIPTION>

- **Source Example**:
```<LANGUAGE>
<SOURCE CODE EXAMPLE>
```

- **Exceptional Cases**: 
  - **Exception Description**: <DESCRIPTION>
  - **Exception Pattern Identification**:
    - <ITEM 1>
    - <ITEM 2>
    - ...
  - **Exception Source Example**:
```<LANGUAGE>
<SOURCE CODE EXAMPLE>
```
  - **Exception Handler Reference**: [SIP-<CATEGORY>-<NUMBER>]
````