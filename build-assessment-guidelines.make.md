# Build Assessment Guidelines - Make

## Overview
Guidelines for assessing Make build systems to ensure comprehensive understanding of project structure, dependencies, and build semantics.

## Guidelines

### Variable Configuration
- **Name**: Compiler and Flag Configuration
- **Description**: Identifies how Make defines compiler variables and compilation flags
- **When to Apply**: When CC, CXX, CFLAGS, CXXFLAGS, or similar compiler variables are defined
- **Source Code Patterns**:
  ```makefile
  CC = compiler_name
  CFLAGS = -flags
  CPPC = cpp_compiler
  CPPFLAGS = -cpp_flags
  ```
- **Projects Applied**: parson

### Target Definition and Dependencies
- **Name**: Target Rules with Dependencies
- **Description**: Documents how Make targets are defined with their dependencies and build commands
- **When to Apply**: When encountering target rules with prerequisites and recipes
- **Source Code Patterns**:
  ```makefile
  target: dependencies
  	command
  	./executable
  ```
- **Projects Applied**: parson

### Phony Target Management
- **Name**: Phony Target Declaration
- **Description**: Identifies targets that don't represent files but actions
- **When to Apply**: When .PHONY declarations are present
- **Source Code Patterns**:
  ```makefile
  .PHONY: target_names
  target_name: dependencies
  	commands
  ```
- **Projects Applied**: parson

### Multiple Build Variants
- **Name**: Multiple Target Variants
- **Description**: Tracks how different build configurations or test variants are handled
- **When to Apply**: When multiple similar targets exist with different compilation flags or defines
- **Source Code Patterns**:
  ```makefile
  target1: sources
  	$(CC) $(FLAGS) -o $@ sources
  
  target2: sources
  	$(CC) $(FLAGS) -DSPECIAL_DEFINE -o $@ sources
  ```
- **Projects Applied**: parson

### Clean Target Pattern
- **Name**: Clean Target Implementation
- **Description**: Documents how cleanup operations are implemented
- **When to Apply**: When clean targets are present for removing generated files
- **Source Code Patterns**:
  ```makefile
  clean:
  	rm -f files_to_remove
  ```
- **Projects Applied**: parson

### Public Interface Identification
- **Name**: Implicit Public Interface Through Source File Patterns
- **Description**: Identifies public interface through consistent source file usage patterns across multiple targets and cross-language compatibility testing
- **When to Apply**: When library source files are consistently used across all build targets while test files are clearly separated
- **Source Code Patterns**:
  ```makefile
  # Library sources used consistently across targets
  target1: tests.c library.c
  	$(CC) $(CFLAGS) -o $@ tests.c library.c
  
  target2: tests.c library.c
  	$(CPPC) $(CPPFLAGS) -o $@ tests.c library.c
  
  # Cross-language compatibility testing suggests public interface
  testcpp: test_files library_files
  	$(CPPC) $(CPPFLAGS) -o $@ test_files library_files
  ```
- **Projects Applied**: parson
