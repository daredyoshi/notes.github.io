# Fundamental Data Types

In C++ we typically work in "byte-sized" chunks. The smallest unit of memory is a **binary digit** aka **bit**. All data types are represented by these. These are assigned to **memory addresses** so that we can find and reuse them. Bits are very small, so each bit doesn't get it's own address. Only for every **byte** of memory are addresses allocated. Each byte is 8 bits. 

**Data types** tell the compiler how to interpret the contents of a block of memory. Each object allocates memory, and then the compiler and cpu encode that and store it. The **fundamental data types** make up everything in C++ that gets stored. 

| Types         | Category          | Meaning           | Example   |
| ------------- | ----------------- | ----------------- | --------- |
| float, double, long double  | Floating Point | fractional number | 3.14159   |
| bool          | Integral (Boolean)| true or false     | true      |
| char, wchar_t char8_t (C++20), char16_t (C++11), char32_t (C++11) | Integral (Character) | a single character of text | 'c' |
| short, int, long, long long (C++11) | Integral (Integer) | positive and negative whole numbers including 0 | 64 | 
| std::nullptr_t (C++11) | Null Pointer | a null pointer | nullptr|
| void | Void | no type | n/a |

Integral means "like and integer". They are stored in memory as integers. Newer types often have the _t suffix. 

## Object sizes
Depending on the object type, it might consist of more than one bit. Each bit can be either 1 or 0. The values that an object can hold goes up exponentially by the amount of bits it consists of. 3 bits can hold 8 possible values.
You can find the size of variables using the **sizeof operator**


## Void
Means "no type"! It's used for fucntions that don't need to return anything. It's also used for void pointers. 

## Signed integers

The **sign** of an integer determines if it is positive or negative. By default all ints are **signed** which means that the sign is preserved. 
```cpp
short s;
int i; 
long l;
long long l;
```

#### Signed integer ranges


| Size/Type | Range | 
| -- | -- |
| 8 bit signed | -128 to 127 |
| 16 bit signed | -32,768 to 32,767 |
| 32 bit signed | -2,147,483,648 to 2,147,483,647 |
| 64 bit signed | -9,223,372,036,854,775,808 to 9,223,372,036,854,775,807 |

or an n-bit signed variable has a range of $-(2^{n-1})$ to $2^{n-1} -1$

If you try to assign an inter that is out of range you encounter **integer overflow**. This leads to undefined behavious. 

If you try to divide integers the fractional is always dropped! It's always rounded down. 

The best types for these are std::int_fast#_t if going for speed or std::int_least#_t if going for size. 

## Unsigned integers

**Unisgned integers** can only hold non-negative whole numbers. 
```cpp
unsigned short us;
unsigned int ui;
unsigned long ul;
unsigned longlong ull;
```

#### Unisgned integer range
| Size/type | Range | 
| -- | -- |
| 1 byte unsigned | 0 to 255 | 
| 2 byte unsigned | 0 to 65,535 | 
| 4 byte unsigned | 0 to 4,294,967,295 |
| 8 byte unsigned | 0 to 18,446,744,073,709,551,615 |


If you store a number that's too high or too low you get overflow. It stores the ramainder and essentially loops around. Same with the other direction. Ghandi from Civilization was suspected to be too peaceful and then wrap around to become very agrressive in the neuclear age. 

Generally it's reccomended not to use unsigned ints, because it becomes confusing to read the code. Especially if mixed with signed ints. 

Cases where it's alright to use unsigned ints:

* Bit manipulation 
* Certain cases of array indexing
* Performance limitation on limited hardware

## Fixed-width integers and size_t
Some systems allow for even larger variables. That is why we now have **fixed-width integers** in stdint.h that are guaranteed to have the same size on any architecture. You should generally not use these, because sometimes larger ints can have better performance. 

To compensate for this there are also now std::int_fast#_t (where # = 8, 16, 32,or 64) and std::int_least#_t. 

Favor these when you need an integer guaranteed a certain size. 
Even with these avoid the 8 bit ones as they are often treated as chars. 

### best practices

Use int wheever it's under 6k for sure.
If you want a variable guaranteed a size use std::int_fast#_t

Avoid:
* Unsigned types
* 8-bit fixed width integer types
* compiler-specific fixed-width integers.

## Scientific Notation

**Scientific notation** is a shorthand for writing lenghy number. 1.2 x $10^4$ evaluates to 12,000. 
```
Start with 42030
Slide decimal left 4 spaces : 4.2030e4
No leading zeros to trim: 4.2030e4
Trim trailing zeros: 4.203e4 (4 significant digits)
```
After applying Scientific notation you are left with **significant** digits, and these determine the number's precision. 

## Floating point numbers

**Floating point** is there to store *very* large numbers or numbers with a fractional components. *Floating* refers to the fact that the dcimal point can support a variable number of digits before and after the decimal points. The three different types of floating point data are **float**, **double**, and **long double**. 

When defined floating point literals, always include at least one decimal point for the compiler. 
```cpp
int x{5}
double y{5.0} // 5.0 is a floating point literal (no suffix means double float type by default)
float z{5.0f} // 5.0 is a floating point literal, f suffix means float typ
```

Always make sure the type of your literals match the type of the variables they're being assigned to or used to initialize. Otherwise an unnecessary conversion will result, possibly with a loss of precision. 

Be wary of accidentally assinging integer literals where floating point literals should be used. 

The **precision** of a floating opint number defined how many significant digits it can represent. A float typically can represent 7 significant digits. This is why it's generally reccomended to use doubles unless there's a good performance reason to use floats. 

Because of the limited precision of float, you can often encounter rounding errors - even when working with doubles. This can make comparing floats difficult. The errors aren't the exception -- they're the rule. Be wary of using floatnig point number for financial or currency data. 

Two special categories for float numbers are **Inf** and **NaN** (Not a Number). they are only avaliable if the compiler uses a specific format. It can happen when dividing by zero.

## Boolean values

A **bool** is a variables that can only have two possible values: true and false. 
```cpp
bool b1 { true };
bool b2 { !false }; // initialize to true
b1 = false;
bool b3 {}; // default initialize to false 
```
Booleans get printed as 0 for false and 1 for true.

Functions that return a boolean should start with is
```cpp
bool isEqual(int x, int y){
    return ( x == y )
}
```

## If Statements

Allows us to execute statements if some condition is true. 
```cpp
if(condition) true_statement;

if (condition)
    true_statement;

if (condition){
    statement_a;
    statement_b;
}
if (condition)
    statement_a;
else if (condition)
    statement_b;
else:
    statement_c;
```

## Chars

**ASCII** is American Standard Code for Informaion Interchange. 
```cpp 
char ch2{ 'a' }; // initialize with code point for 'a' (integer 97)
char ch1{97}; // initialize with integer 97 ('a')(not preferred)
```
The fixed width integer int8_t is usually treated the same as a signed char. You can cast chars to ints. This is called a **type cast**
```cpp
char ch{97};
int i = static_cast<int>(ch);
```

Always put stand-alone chars in single quotes 'a', 'b', and strings in double quotes "The quick brown fox" - this helps the compiler optimize better. 

## Literals

A **constant** is a fixed value that may not be changed. **Literal constants** or **literals** are values that are inserted directly into the code. 

You can change the default type of a literal by adding a suffix. Look up **Literal suffixes** if you ever need this. 

Don't use magic numbers in your code. 

## Const variables

Often you want to specify explicitly that a variable is immutable. This is done by defining that variable as const.
```cpp
constexpr double gravity {9.8};
iconst int myValue {x};
```
You can make function parameters const to assure the user that this variable will not be modified by the function. 

There are two types of constants in cpp
* **Runtime constants** - their value can only be resolved at runtime. Things like *userAge* and *myValue*. These should be declared as const. 
* **Compile-time constant** are constants that are set during compile time. These are often hardcoded. These should be declared as constexp.

Instead of using hard coded magic numbers, use constexpr variables. This makes refactoring much easier, and is more descriptive for what this value is representing. It's often useful to have something like a constants.h
```cpp
#ifndef CONSTANTS_H
#define CONSTANTS_H

// define your own namespace to hold constants
namespace constants
{
    constexpr double pi { 3.14159 };
    constexpr double avogadro { 6.0221413e23 };
    constexpr double my_gravity { 9.2 }; // m/s^2 -- gravity is light on this planet
    // ... other related constants
}
#endif
```





