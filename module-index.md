# Module Index

## cmocka

**C Unit Testing Framework with Mock Support**

### Bazel Dependency
```starlark
bazel_dep(name = "cmocka", version = "1.1.5")
```

### Public Targets

#### `//:cmocka`
- **Type**: `cc_library`
- **Description**: Main cmocka library providing C unit testing and mocking framework
- **Headers**: `cmocka.h`, `cmocka_pbc.h`, `cmocka_version.h`
- **Usage**: Link against this target to use cmocka in your tests
- **Example**:
  ```starlark
  cc_test(
      name = "my_test",
      srcs = ["my_test.c"],
      deps = ["@cmocka//:cmocka"],
  )
  ```

### Configuration Notes

#### Compiler Requirements
- Requires C99 or later (`-std=c99`)
- Automatically sets required defines: `_GNU_SOURCE`, `_XOPEN_SOURCE=700`

#### Test Integration
- Use `cc_test` rule type for proper Bazel test integration
- cmocka tests return 0 on success, non-zero failure count on failure
- Framework provides assertion macros: `assert_int_equal()`, `assert_string_equal()`, etc.

#### Platform Support
- Tested on Linux x86_64
- Configuration assumes POSIX-compliant environment
- May require platform-specific configuration for other environments

### Internal Targets (Not for External Use)

#### `//:cmocka_private`
- **Type**: `cc_library`
- **Description**: Private library exposing cmocka internals for testing
- **Visibility**: `//tests:__pkg__` only
- **Purpose**: Allows internal tests to access non-public functions
- **Note**: External users should not depend on this target

### Dependencies
- `rules_cc` (automatically handled by Bazel)
- Standard C library
- POSIX threading support

### Build Features
- Comprehensive test suite (57 tests)
- Support for test failure simulation ("_fail" tests)
- Exception handling tests (Linux-specific)
- Memory allocation testing utilities
