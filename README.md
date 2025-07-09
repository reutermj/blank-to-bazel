# blank-to-bazil

This repo contains an engineered prompt for use with AI agents to convert Make, CMake, and Autotools build definitions to bazel build definitions.

Currently sourcing projects from: https://project-awesome.org/inputsh/awesome-c

## Converted projects

* https://github.com/reutermj/sds
* https://github.com/reutermj/parson
* https://github.com/reutermj/wjelement
* https://github.com/reutermj/cmocka
* https://github.com/reutermj/cmp
  * first project that depended on another: cmocka
  * limitation: didnt convert any targets that depended on gperftools
* https://github.com/reutermj/libcrc

## Future projects

https://github.com/reutermj/jsmn
* has a clang-format and clang-tidy target
* might be good to learn how to prompt it to use: https://github.com/aspect-build/rules_lint

https://github.com/reutermj/yajl
* this one does a bunch of weird things in the cmake
* might be a good target for handling really bad build definitions

https://github.com/reutermj/simdjson
* the implementation of this `#include`s a bunch of .cpp files

https://github.com/PeterScott/murmur3
* at this point, too much of a low hanging fruit, but would be nice to do at some point
