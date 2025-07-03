# blank-to-bazil

This repo contains an engineered prompt for use with AI agents to convert Make, CMake, and Autotools build definitions to bazel build definitions.

Currently sourcing projects from: https://project-awesome.org/inputsh/awesome-c

## Converted projects

* https://github.com/reutermj/sds
* https://github.com/reutermj/parson
* https://github.com/reutermj/wjelement

## Future projects

https://github.com/reutermj/jsmn
* has a clang-format and clang-tidy target
* might be good to learn how to prompt it to use: https://github.com/aspect-build/rules_lint

https://github.com/reutermj/yajl
* this one does a bunch of weird things in the cmake
* might be a good target for handling really bad build definitions

https://github.com/reutermj/simdjson
* the implementation of this `#include`s a bunch of .cpp files
* 
