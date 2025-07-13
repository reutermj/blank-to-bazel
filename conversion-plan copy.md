# Create conversion plan

For every build system assessed:
1. Read the existing `build-assessment.<project>.<source build system>.md` to understand the current build system structure and components.
2. Read the existing `conversion-plan-guidelines.<source build system>.md` to understand established conversion patterns and best practices.
3. Create a concrete conversion plan that transforms each element from the build assessment into equivalent Bazel constructs.
4. Conversion Plan Creation: create a `conversion-plan.<project>.<source build system>.md` file that provides a complete conversion strategy.
5. Guideline Maintenance: update `conversion-plan-guidelines.<source build system>.md` files to preserve conversion knowledge for future projects.

---

## Conversion Plan Requirements (`conversion-plan.<project>.<source build system>.md`)

### Project Level
- **Build System Coverage**: If a project has multiple build systems, produce a distinct conversion plan for each build system
- **Complete Mapping**: Every item identified in the original build assessment must be accounted for in the conversion plan
- **Traceability**: Maintain clear mapping between assessment items and conversion items

### Conversion Item Level

For every item from the build assessment, document:

#### Elements
- **Assessment Reference**: Direct reference to the specific item in `build-assessment.<project>.<source build system>.md` being converted
- **Conversion Description**: Brief description of what transformation is being performed
- **Bazel Implementation**: Complete Bazel source code that implements the conversion
- **Conversion Rationale**: Explanation of why this specific conversion approach was chosen
- **Guideline Reference**: Reference to the specific guideline(s) from `conversion-plan-guidelines.<source build system>.md` that this conversion follows
- **Non-Conversion Justification**: If an assessment item should not be converted to Bazel, explicitly state this with reasoning

#### Guidelines
- **Completeness**: Address every build step, dependency, input, output, and parameter from the original assessment
- **Accuracy**: Ensure Bazel code is syntactically correct and functionally equivalent
- **Consistency**: Apply conversion patterns uniformly across similar build constructs

### Dependency and Interface Preservation
- **Dependency Mapping**: Ensure all build step dependencies identified in the assessment are preserved in Bazel target dependencies
- **Public/Private Interface**: Maintain the public and private interface distinctions identified in the build assessment through appropriate Bazel visibility rules and target organization

---

## Conversion Plan Guidelines Requirements (`conversion-plan-guidelines.<source build system>.md`)

#### Elements
- **Name**: Descriptive name of the conversion guideline
- **Description**: What build system pattern this guideline converts and the resulting Bazel pattern
- **Source Build System Example**: Concrete code example from the source build system showing the pattern being converted
- **Bazel Example**: Complete Bazel code example showing the converted result
- **Applicability**: Specific source build system patterns or features that trigger using this guideline
- **Exceptions**: Specific cases where this guideline should not be applied, with alternative approaches. If none, leave out. Dont speculate about exception.
- **Projects Applied**: List of projects where this conversion guideline was successfully used

#### Guidelines
- **Preserve ALL existing guidelines** from the current file
- **Add new guidelines** based only on conversion patterns discovered in the current project
- **Update existing guidelines** if new evidence supports refinement or reveals exceptions
- **Evidence-based**: Only create guidelines based on actual conversion challenges encountered, not theoretical best practices
- **Practical Focus**: Emphasize guidelines that solve real conversion problems over abstract principles
- **General Purpose**: Guidelines should be applicable to any build system conversion, not project-specific
- **Generic Code Samples**: Both source build system examples and Bazel examples should use generic placeholders (e.g., `library_name`, `source_files`, `target_name`) rather than project-specific names
