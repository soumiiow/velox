# Dynamic Loading of Velox Extensions

This generic utility adds extensibility features to load User Defined Functions (UDFs), connectors, or types without having to fork and build Velox, through the use of shared libraries.

## Getting started
1. Create a cpp file for your dynamic library
For dynamically loaded function registration, the format followed is mirrored of that of built-in function registration with some noted differences. Using [MyDynamicTestFunction.cpp](tests/MyDynamicTestFunction.cpp) as an example, the function uses the extern "C" keyword to protect against name mangling. A registry() function call is also necessary here.

2. Register functions dynamically by creating .dylib (MacOS) or .so (Linux) shared libraries.
These shared libraries may be made using CMakeLists like the following:

* Linux

    ```
    add_library(name_of_dynamic_fn SHARED TestFunction.cpp)
    target_link_libraries(name_of_dynamic_fn PRIVATE xsimd fmt::fmt)
    ```
    Above, the xsimd and fmt::fmt libraries are required for all necessary symbols to be defined when loading the TestFunction.cpp dynamically
* MacOS:
    ```
    add_library(name_of_dynamic_fn SHARED TestFunction.cpp)
    target_link_libraries(name_of_dynamic_fn PRIVATE fmt::fmt Folly::folly gflags::gflags xsimd)
    ``` 

    Additionally, the below flag allows symbols to be resolved at runtime:
    ```
    target_link_options(name_of_dynamic_fn PRIVATE "-Wl,-undefined,dynamic_lookup")
    ```