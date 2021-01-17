These are notes from going through the learning on [this site](www.learncpp.com)

They are not comprehensive, but purely based on what I found useful.

## Introduction to Programming languages
Assembly translates machine code for the hardware. C++ is designed to be hardware agnostic - thus considered a high-level language. 

    Assembly: mov al, 061h
    C++: a = 97

The process of translating high level code into runtime code is either compiling or interpreting. **Compilers** read source code and generate executables that can then be run. **Interpreters** directly execute instructions in the source code without requiring a compilation stage. They are generally less efficient. 

## Introduction to C/C++

The C language was developed as a language to write operating systems with, a **systems programming language**. Most of the unix operating system is written in C. 

### Philosophy

The underlying design philosophy of C and C++ can be summed up as "trust the programmer" - which allows people to develop truly terrifying code. Knowing what not to do is almost as important as knowing what to do. 

### Workflow 
![workflow](./Development-min.png)
Step 2 is very important. Too often you will have an idea and start programming immediately. Here's a list of requirements for good software:

1. Structured well (not complicated or confusing)
2. Documented well (especially what assumptions are being made)
3. Modular design (interchangable parts)
4. Robust and able to give meaningful error messages

20% of the time is spent writing the program, 80% is maintinence. Better to spend a bit more time up front. 

!!! warning
    Name code files <name>.cpp 

    This means that the file is a source file! Some people us .cc but it's not a standard.

### Compiling 

The compiler goes through each (.cpp) file and does two things:

1. Checks that the code follows the rules of C++
2. Translates the code into machine language file called **object file**. The object file ends in ```.o```

### Linking Object Files and Libraries
After the compiler creates the appropriate code the linker brings in the correct libraries. This tutorial series does not cover a Makefile which is one of the things I was hoping to learn. It's considered "advanced". 

## Writing your first program

This is a simple helloworld example. 

## Configuring your compiler: Build configurations
A **build configuration** (aka a **build target**) is a colelction of project settings that determine how things in the project will be built. 

Because I am using vim, I've folowed [this guide](https://dane-bulat.medium.com/vim-setting-up-a-build-system-and-code-completion-for-c-and-c-eb263c0a19a1) to set up vim with cmake. 

I also used his [previous guide](https://dane-bulat.medium.com/powerline-on-linux-an-integration-guide-c097831106f6) to futher extend vim with some more savvy plugins.

The guides also go over linking libraries with cmake, which was very useful. I now have a project root and buildsystem for following alowing with these tutorials. 
