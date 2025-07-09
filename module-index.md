# Module Index

## cmp
```starlark
bazel_dep(name = "cmp", version = "1.0.0")

# public targets
cc_library(
    name = "cmp",
    hdrs = ["cmp.h"],
    visibility = ["//visibility:public"],
)
```

## cmocka
```starlark
bazel_dep(name = "cmocka", version = "1.1.5")
git_override(
    module_name = "cmocka",
    remote = "https://github.com/reutermj/cmocka.git",
    commit = "15a3a5365b61849190af9cdb7494053889604bf2",
)

# public targets
cc_library(
    name = "cmocka",
    hdrs = [
        "include/cmocka.h",
        "include/cmocka_pbc.h",
        "include/cmocka_version.h",
    ],
    includes = ["include"],
    visibility = ["//visibility:public"],
    ...
)
```
