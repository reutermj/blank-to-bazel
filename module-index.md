# Module Index

## cmocka
```starlark
bazel_dep(name = "cmocka", version = "1.1.5")
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
