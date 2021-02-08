# Intorduction to the basics

At the beginning of the top CMakeLists.txt file of every project goes a line for the minimum cmake version required. 

[cmake_minimum_required](https://cmake.org/cmake/help/latest/command/cmake_minimum_required.html)
```cmake
cmake_minimum_required(VERSION 3.1)
```

Syntax wise cmake commands are case insensitive. Because of this it's common practice to use lowercase for them. VERSION is a special keyword for this function, and the value of 3.1 is written into the variable .

This line is important! It specifies how your cmake policies are defined. For example if you set minimum_required to VERSION 2.8, you'll get the wrong linking behaviour on macOS, even if the user happens to use a newer cmake version. It tells cmake how to interpret your files. This is to always preserve backwards compatibility. You can read all the policies [here](https://cmake.org/cmake/help/latest/manual/cmake-policies.7.html). As of cmake 3.12, this also supports a range of versions. 

```cmake
cmake_minimum_required(VERSION 3.7)

if(${CMAKE_VERSION} VERSION_LESS 3.19)
    cmake_policy(VERSION ${CMAKE_MAJOR_VERSION}.${CMAKE_MINOR_VERSION})
else()
    cmake_policy(VERSION 3.19)
endif()
```

New projcts should use the above setup. This supports down through cmake 3.7 for older distributions, and adjusts the cmake policies if it's a lower version accordingly. 

The next thing every top level cmake file has is this line:

```cmake
project(MyProject   VERSION 1.0
                    DESCRIPTION "Very nice project"
                    LANGUAGES CXX)
```

Strings are quoted, whitespace doesn't matter. Next you will want to add a **target**. Targets are used in every build system, and they determined rules for things to build. Targets, can be executables (the program to run), libraries (things for other programs to link to), or as simple as copying files from a-z. 

