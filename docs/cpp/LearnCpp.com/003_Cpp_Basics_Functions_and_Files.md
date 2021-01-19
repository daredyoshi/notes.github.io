# C++ Basics: Functions and Files

As defiend in the previous section: A **function** is a *reusable* sequence of statements designed to do a particular job. Functions allow programs to be split into modular chunks that are bestter for organizing, testing and using. Functions users write are called **user-defiend functions**. 

The **function body** is the part between the curly braces.

It's important to note that in C++ nested function are not supported. This is different than python.

Functions can give a **return** value - the result of a function. When you defined a function, the first word is always the **return type**. The **return statement** inside the function indicates the specific **return value**. The process of copying the return value from the function back to the caller is called **return by value**. 

Functions can return a **void** value if there is no other sensible value they might want to reuturn. Only these functions do not require a return statement. 

The main function always returns an integer. This value returned frmo main is called **status code**, **exit code** or **return code** and is used to determin if the program ran successfully. You should return 0 if the program ran normally. 

If a function has a non-void return type, it *must* return a value of that type. Undefined behaviour ensues if you don't. 

DRY stands for daerdyoshi and also "Do not Repeat Yourself" - which basically means that you shouldn't have duplicate code. I'd like to argue and say that duplicate code can be valuable when you want your code to be more readable, as this concept can be taken too far.

## Introduction to function paraemters and arguments

A **function parameter** is a variable that is is designed to have a function **argment** bound to it as an input to the function. Parameters are the variables used in the function, argments are the variables that are given outside the fucntion. The process of passing an argument to a parameter is called **pass by value**. You can use return values as arguments. 

Don't make function calls where argment order matters.

## Introduction to local scope

Function parameters and variables defined in the function body are called **local variables**. They are accessible only within the function body. They are all automatically destroyed when the function ends. The time between an object's creation and destruction is considered it's **lifetime**. 

The **scope** of an identifier determines where the identifier can be accessed. When it can be accessed we say the identifier is **in scope**, and if not it is **out of scope**. Scop is checked at compile time, and the program won't compile if trying to access an identifier outside of it's scope.

They say to defined local varialbes as close to their first use as possible, but I prefer to define them at the top of whatever scope they are used in with nice descriptive code. 

**Functions should do only one task**

## Foward declations and definitions

You can't call a function before it is defined in C++. To work around this you can finess your functino order or use **forward declaration**. Forward declaration allows us to tell the compiler about the existence of an identifier before defining the identifier. 

The way to write a forward declaration for a function is using a declaration statement called a **function prototype**. It's the same as desclaring a functino but without the function body.

```int add(int x, int y)```

These are generally defined in the header files. 

**Definition** and **declaration** are often used interchangably. The key difference is that when you define a identifier you are actually implementing a value to it, but declaring it only creats the identifier. Definitions are needed to satisfy the linker. An identifier without definition will cause the linker to error. 

The **one definition rule (ODR)** states that things may only be defined once.

1. When given *file*, a function, object, type, or template can only have one def
2. When given a *program*, an object or normal function can have only one def. 
3. types, templates, inline functions and variables are allowed to have identical defs in different files. 

Note that it's possible to overload functions if you want to re-use them with differnt inputs. 

## Programming with multiple code files

If you just take your function into an external file it won't automatically work because it needs to get added to your project lol. I set up a new project and added the file manually in my CMakeLists.txt - though I'm sure this can be done in a much better way.

## Naming collisions and an introduction to namespaces

If two functions are named the same you have a **naming collision** (or **nameing conflict**). Luckily this will be detected by the linker. This can happen from two files in the same program or two or more functions added via an #include. You keep function names from conflicting through the magic of **namespace**

Namespaces are regions that allow you to declare names inside them for the purpose of disambiguation. They provide a **namespace scope** to the names declared inside them. A name defined in a specific namespace won't be mistaken for an identical name declared in another scope. 

The default namespace is the **global namespace** or **global scope**. Originally in C++ everything in std:: was in the global scope, but this got changed. 

To tell the compiler to look for a function in a specific namespace you can either explicityly use the namespace qualifier std:: or ```using namespace std```. The second method is called a **using directive**, which tells the compiler to checka spedified namespace when trying to resolve an identifier that has no namespace prefix. It's greatly preferred to write out the namespaces in order to be more explicit and because it's easier to run into conflicts otherwise. 

## Introduction to the preprocessor

Prior to compilations, code goes thruogh a **translation** phase and then becomes a **translation unit**. The most noteworthy translation phase is the **preprocessor** which is basically it's own program changing the text in each code file. When the preprocessor runs through a file, it is searching for **preprocessor directives**, which aer instructions that start with a # and end with a newline instead of a semicolon. 

The most common preprocessor directive is the **#include**. These directives are replaced by the entire code of that file in memory as the preprocessor. This is used almost excusively with header files. 

The **#define** directive can be used to create a **macro**: a rule that defines how input text is converted into replacement output text. There are two types of macros: object-like and function-like. Function like macros are generally consered dangerous, and anything they do can be done by a normal function. 

### Object-like macros
Object like macros have two forms:
```
#define identifier
#define identifier substitution_text
```

When the preprocessor encounters the second one, the identifier is replaced with the substitution text. The convention for the identifier is ALL_CAPS. These provide a cheaper alternative to global constant variables, but those times are GONE so you only really see them in legacy code. 

Object like macros with no substitution text simply remove that word. 
If the word is part of another preprocessor directive it is not removed.

### Conditional compilation

There are quite a few conditional compilation directives, the ones most used are **#ifdev, #ifndev and #endif**. These allow the preprocessor to check what has already been defined. So if a certain file has already been included you are not including it twice. 

### #if 0
This basically works as a multi line comment paird with #endif


Defines are not part of local/global scope, but part of the file scope. 

## Header files

These are files designed for the purpose of forward declaration. They usually have a .h extention, but sometimes have .hpp. They allow us to put declarations in one location and then impor them whereever we need them. The can save a lot of typing in multi-file programs.

When you #include a header file, that files is added to your current file at compilation time. Then, later, the linker links those function to their definitions. 

Header files should generally not contain functions and variable definitions. An exception can be made for symbolic constants. 

Header files paired with code files shoul have the same name. Header files should be included in their corresponding src files. 

Use double quotes to include header files that you've written or are in the current directory, use angled brackets for others. 

For including the standard library, leave out the .h

Do not include header files by relative pathing. It's better to tell the compiler which directories to add. 

Header files may include other headerfiles, but should do so explicitly and not rely on those header files including other headfiles. 

Order your #includes like this:

1. Your own user-defined headers
2. Third party headers
3. Std headers

More header best practices

* Always include header guards.
Do not define variables and functions in header files except for global constants.
* Give the header files the same name as the source files
* Each header has one specific job and should be as independent as possible
* Try to include as little as possible and be mindful about it
* Each header should compile on it's own. 
* Only #include what you need.
* Do not #include .cpp files. 

## Header guards

Header guards protect the ODR (One definition rule). **header guards aka include guards** are conditional compilation directives. 

```
#ifndev SOME_UNIQUE_NAME_HERE
#define SOME_UNIQUE_NAME_HERE

// your declarations

#endif
```

This prevents the same function from being compiled multiple times. You should be usinging more comlex names for your header guards than just the filename. This is a good template:

```<PROJECT>_<PATH>_<FILE>_H```

Note that if you're dumb and you write your functions into your header files they can be included multiple times. Example: main and geometry both include poly ops. If the head files only contains forward definitions, this is no problem, otherwise you will get a conflict. 

