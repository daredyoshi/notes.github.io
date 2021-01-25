# Object Scope and Conversions

Code blocks work just like in vex. A **compound statement** is when you have multiple statements seperated by ; after an if statement. This is also called a **block**. You should keep the nesting level of your functions to 3 or less. If your function has a need for more, consider abstracting that to anther function. 

In cpp you manually defined **user-defined namespaces** - these allow you to have duplicate named functions that are still organized properly. It's very simple to set them up:
```cpp
namespace foo \\defnie a namecpace named foo
{
    int doSomething(int x, int y){
        return x + y;
    }
}
```

Now you call doSomething like this:
```cpp
int dosomething(intx, int y); //forward declaration

int main(){
    std::cout << foo::doSomething(4,3) << '\n';
    return 0
}
```
This example is incorrect and wouldn't compile, because you should define doSomething in it's own header file. 

Namespaces can be nested within other namespaces.

You can create **namespace aliases** which allow you to temoprarily shorten long sequences of namespaces into something shorter. These can be useful for moving functionality between namespaces. 

```cpp
namespace foo:goo{
    int add(int x, int y)
    {
        return x = y;
    }
}

int main(){
    namespace boo = foo::goo; // bo now refers to foo:goo
    std::cout << boo::add(1, 2) << '\n';
    return 0;
}
```

Use namespaces to seperate application-specific code from code that might be re-usable in a different setting. Math functions shouldn't be part of your core namespace for example.

## Local Variables

Local variables have **block scope** - they are *in scope* from their point of definition to the end of the block they are defined in. The one exception is for exception handling in things like try blocks where they are avaliable after the block has errored. 

All variable names within a scope must be unique
Local variables have **automatic storage duration** which means that they are automatically removed from memory after the function is complete. For this reason local variables are sometimes called **automatic variables**. 

Nested block are considered within scope of their outer blocks. 

Local variables have no **linkage**. Linkage determines whether other declarations of that name refer tothe same object or not. Each local variable declaration is a unique object. 

You should always defined variables in the most limited scope possible. 

## Global Variables

These are variables declared *outside* a function. 

Global varialbes are declared at the top of a file, below the inclues, but above any code. Considering using a "g" or "g_" prefix for all global variables to remind yourself of their scope. 

Global variables have **file scope** also known as **global scope** or **global namespace scope**. They have a **static duration** - this means they are created when the program starts and destroyed when the program ends. 

You can declare them as constant which can be very useful. Non-constant global variables can easily lead to unexpected results if you forget where you change them. 

## Variable shadowing (name hiding)

If you define a variable in an inner block that has the same name as a variable from the outer block it **shadows** the variable of the out block which means basically override it for the duration of the inner block's scope. 

You can do the same thing to global variables. 

In general you should avoid this always. But yea that's what happens. 

## Internal linkage

Global variables can have internal linkage or external linkage. an identifier with **internal linkage** can be seen and used within a single file, but not in other files. 
```cpp
static int g_x; // non-constant globals have external linkage by default, but can be given internal linkage via the static keyword

const int g_y {1}; // const globals have internal linkage by default

constexpr int g_z { 2 }; // constexpr globals have internal linkage by default
```

The use of the *static* keyword is an example of a **storage class specifier** which sets both the name's linkage and its storage duration ( but not it's scope ). 

Linkage is a property of the identifier. An identifier's linkage determines whether other declarations of that name refer to the same object or not. 

## External Linkage
Through the power of forward declaration you can use a single variable in multiple files with **external linkage**. Functions have external linkage by default - they are also global varaibles. Global variables with external linkage are sometimes called **external variables**. 
```cpp
int g_x { 2 }

extern const int g-y { 3 };// const globals can be defined as extern
extern constexpr int g_z { 3 }; // constexp globals can be defined as extern
```
Non const global variables are external by default. 

You have to place forward declaration in all files where you plan to use the external variable

It makes no sense to declare constexpr as they can not be forward declared. 

If you want to defined an uninitialized non-const global variable, do not use the extern keyword, otherwise C++ will think you're trying to make a forward declaration for the variable. 

Initialization of globals happens as part of the program startup, before the main(). It has two phases

1. Static Initialization. All the global variables with constexpr are evaluated, as well as all global variables without initializers are zero-initialized. 
2. Dynamic Initialization. All the other global variables are initialized. i

Dynamic initialization of global variables causes a lot of problems. Avoid whenever possible. 

## Global constants and inline variables

Pre-C++17 this is how you would define all the constants for your project:
constants.h
```cpp
#ifndn CONSTANTS_H
#define CONSTANTS_h

namespace constants{
    constexpr double pi { 3.14159 };
    constexpr double avogadro { 6.020413e23 };
    constexpr double my_gravity { 9.2 };
}
#endif
```
This is alright for smaller programs, but during compiletime this header will be added to every single file including it. This means that these variables will be initialized in every file as well. A way that scales better looks like this

constants.cpp
```cpp
#include "constants.h"

namespace constants{
    extern const double pi {3.14159};
    extern const double avogadro { 6.0221413e23 };
    exter const duoble my_gravity { 9.2 };
}
```
constants.h
```cpp
#ifndev CONSTANTS_H
#define CONSTANTS_H

namespace constants{
    extern const double pi;
    extern const double avogadro;
    extern const my_gravity;
}
#endif
```
This forward declares them in the header file, so that they are only declared once at runtime and then linked. 

In C++17 you would define them as inline variables. Which would once again look like the first example. If your compilier is C++17 compatible this method is preferred. 

## Why global variables are evil

Many developers believe non-const global variables should be avoided completely! The danger is that their values can be changed by any function that is called. This can make changes to the program unpredictable if you don't know exactly what happens in each function. Use local variables instead of global variables whenever possible. 

Possible uses for globals:

* Log file. 
* constants

If you do decide to use globals protect yourself like this:

* prefix them with "g_"
* encapsulate the variable. (so that is can only be accessed from within the file it's decalred in)
* pass in the globas as variables in the functions to be more explicit. 

## Static local variables

Static has many meanings in cpp. Global variables have a *static* duration, and internal linkage if defined with the static keyword.  

You can create **static local variables** by using the static keyword on them. This means that their duration is changed from automatic to statuc. So now it won't be destroyed until the end of the program. This means it will retain it's value even after it goes out of scope!

Static initializers are only executed the first time. A good practice is to use "s_" to prefix static local variables. They are commonly used for unique ID generators. 
```cpp
int generateID(){
    static int s_itemID{ 0 };
    return s_itemID++;
```
This will return a unique id for the duration of the programs' runtime. These are cool, but don't overuse them because they make code harder to follow. Avoid static local variables unless the variable never needs to be reset. 

A great review of these can be found [here](https://www.learncpp.com/cpp-tutorial/scope-duration-and-linkage-summary/)

## Typedefs and type aliases

You can create an alias for a data type, and use that name instead of the actual type name while still referring to the same **type definition**.

It's good to suffix type defs with "_t"
```cpp
typedef double distance_t; // define distance_t as an alias for type double
using distance_c = double;

//the following two statements are not equivalent
doubld howFar;
distance_t howFar;
distance_c howFar;
```

The using keyword has no correlation to how using is incorporated for namespaces. This aliasing of types can help you make your code more explicit by adding more descriptive identifier type names. It can also help make our code easier to refactor. They also help for platform independent coding. 

Use aliases (using <name> = type) over typedefs and use them liberally to help document your code. 

## The auto keyword

This has great potential for misuse! Avoid using type inference for function return types. **Type inference** tells the compiler to infer the variable's type from the initiliazer's type.
```cpp
auto d{ 5.0 }; \\ will be type double
auto i{ 1 + 2 }; \\ evaluates to in so i will be type int
```

## Implicit type conversion (coercion)

The process of converting a value from one data type to another is called **type conversion**. This is done automatically if the compilier knows how to for the given types (**Implicit/automatic type conversion**). Converting to a larger numeric data type is called **numberic promotion** or widening. Converting to a smaller numeric data type, or between data types is called **numeric conversion**. **Narrowing conversions** are when data is lost. Note that if you are converting a very large out of range number to a smaller type you are very likely to get overflow. 

## Explicit type conversion (casting) and static_cast

You should avoid c-style casts
```cpp
int i1 { 10 };
int i2 { 4 };
float f { (float)i1 / i2 };
```

In C++ there is an operator called **static_cast** which is the preferred method of converting types. 
```cpp
int i1 { 10 };
int i2 { 4 };
// convert an int to a float so we can get floatnig point division rather than integer division
float f { static_cast<float>(i1) / i2 }
```

## Unnamed and inline namespaces

An **unnamed namespace** is a namespace without a name. The functions in it are considered part of the parent namespsace. This is still useful because all of the identifiers inside an unnamed namespace are treated as if they had internal linkage. Using functions from an unnamed namespace is the same as static functions. 

An **inline namespace** is a namespace that is often used to version content. Everything in it is also considered part of the parent namespace, but the inline namespace does not give everything internal linkage. This can save a lot of refactoring time. 









