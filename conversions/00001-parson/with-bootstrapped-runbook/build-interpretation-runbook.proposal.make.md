# Build Interpretation Runbook Maintenance Proposal - Makefile

This document proposes improvements and new interpretation patterns for Makefile build systems based on the analysis of the Parson JSON library project.

## Updated Maintenance Patterns

### [IP-MAKE-MAINTENANCE-001-REVISION] - Incomplete Cleanup Pattern
**Pattern Description**: Provides cleanup functionality that removes some but not all generated files from build targets. This pattern indicates incomplete maintenance where cleanup doesn't match the scope of targets that generate files, potentially leaving build artifacts that should be removed.

**Pattern Identification**: 
- `clean:` target with selective file removal
- `rm` commands targeting specific files or patterns
- Missing cleanup for some executable targets defined in the Makefile
- Potential mismatch between generated files from targets and cleanup scope

**Source Example**:
```makefile
all: test testcpp test_special

test: tests.c library.c
	$(CC) $(CFLAGS) -o $@ tests.c library.c
	./$@

testcpp: tests.c library.c
	$(CPPC) $(CPPFLAGS) -o $@ tests.c library.c
	./$@

test_special: tests.c library.c
	$(CC) $(CFLAGS) -DSPECIAL_FEATURE -o $@ tests.c library.c
	./$@

clean:
	rm -f test *.o  # Missing testcpp and test_special
```

**Exceptional Cases**: 
- **Exception Description**: Complete cleanup pattern where all generated targets are properly removed in the clean target, indicating thorough maintenance practices.
- **Exception Pattern Identification**:
  - `clean:` target removes all executables created by build targets
  - Cleanup scope matches the scope of file-generating targets
  - No build artifacts left after clean execution
- **Exception Source Example**:
```makefile
clean:
	rm -f test testcpp test_special *.o
```
- **Exception Handler Reference**: [IP-MAKE-MAINTENANCE-002]

### [PROPOSED-IP-MAKE-MAINTENANCE-002] - Complete Cleanup Pattern
**Pattern Description**: Provides comprehensive cleanup functionality that removes all generated files corresponding to the build targets defined in the Makefile. This pattern indicates thorough maintenance practices where cleanup scope matches build target scope.

**Pattern Identification**:
- `clean:` target with comprehensive file removal
- All executable targets from build rules included in cleanup
- Cleanup scope matches the scope of all file-generating targets
- No build artifacts remain after clean execution

**Source Example**:
```makefile
clean:
	rm -f test testcpp test_special *.o
```

**Exceptional Cases**: None encountered yet.
