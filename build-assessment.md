# Assess existing build system

For every build script present:
1. Read the existing `build-assessment-guidelines.<build system>.md` to understand best practices and guidelines for assessing the build system.
2. Assess the existing build system first to understand its structure, dependencies, and build semantics.
3. Build Assessment Creation: create a `build-assessment.<project>.<build system>.md` file that provides a complete analysis of the current build system.
4. Guideline Maintenance: update `build-assessment-guidelines.<build system>.md` files to preserve knowledge for future assessments.

---

## Build Assessment Requirements (`build-assessment.<project>.<build system>.md`)

### Project Level
- **Dependencies**: Document all external dependencies of the project

### Build step level

For every build step, document:

#### Elements
- **Purpose**: What the build step accomplishes
- **Inputs**: All files, variables, and dependencies required
- **Outputs**: All generated files, artifacts, and side effects
- **Key Parameters**: Important configuration options, flags, and variables
- **Dependencies**: Other build steps whose outputs are directly consumed as inputs by this step. Only include actual dependencies where this step cannot execute without the completion of another step. If there are none, explicitly state "None"
- **Guideline Reference**: Reference to the specific guideline(s) from `build-assessment-guidelines.<build system>.md` that this step exemplifies or follows
- **Source code snippit**: Include source code from the actual build script

#### Guidelines
- **Granularity**: Break down large, complex build steps into component parts and explain each component part
- **Formatting**: Maintain consistent and clear formatting throughout

### Public Interface Analysis

Document the public and private interface elements:

#### Public Interface Elements
- **Public Headers and Libraries**: Headers and libraries intended for external consumption, identified through build system mechanisms or common implicit patterns
- **Interface Indicators**: Build system patterns that explicitly mark components as public

#### Private Interface Elements
- **Private Headers and Libraries**: Headers and libraries for internal use only, identified through build system mechanisms or common implicit patterns
- **Interface Indicators**: Build system patterns that indicate internal-only components

#### Analysis Guidelines
- **Evidence-based**: Only identify public/private distinctions based on actual build system patterns, not assumptions
- **Build System Specific**: Use build system-specific indicators rather than general naming conventions

## Guideline Maintenance Requirements (`build-assessment-guidelines.<build system>.md`)

#### Elements
- **Name**: Descriptive name of the guideline
- **Description**: What this guideline helps identify or assess
- **When to Apply**: Specific build system patterns or features that trigger this guideline. Only include if encountered. Do not add speculatively.
- **When to Avoid**: Specific build system patterns or features that trigger avoiding this guideline. Only include if encountered. Do not add speculatively.
- **Source Code Patterns**: Generic code examples showing the pattern. Focus on creating general purpose code examples and not examples specific to any one build.
- **Projects Applied**: List of projects where this guideline was useful. If you found a guideline to be useful during the current assessment, add the current project to the list.

#### Guidelines
- **Preserve ALL existing guidelines** from the current file
- **Add new guidelines** based only on patterns discovered in the current project
- **Update existing guidelines** if new evidence supports refinement
- **No speculation**: Only create guidelines based on actual build system patterns observed. Avoid adding theoretical best practices not encountered
