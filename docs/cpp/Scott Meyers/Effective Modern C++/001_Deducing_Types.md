# Deducing Types

Before C++11 there was only one set of rules for type deduction: function templates. C++11 adds two more: `auto` and `decltype`. The advantages of type deduction is that it makes code much easier to refactor, but it can also make it harder to understand, as it's less verbose.

## Item 1: Understand template type deduction

Templates type deduction is a tremendous success in C++ because most people don't understand how it works, and can use it anyway. Template type deduction is the basis for `auto`, so now is the time to learn how it actually works. 

Given the following pseudocode:

```cpp
template<typename T>
void f(ParamType param);
// a call can look like this:
f(expr);
```

Compliers will use expr to deduce a type for T and a type for `ParamType`. Often these are not the same type, as param might be const or reference, while T is the type without adornments. T's type is not just dependent on the type of expr, but also on the form of `ParamType`. There are three cases where the form of `ParamType` can change T

* `ParamType` is a pointer or reference type, but not universal reference. 
* `ParamType` is a universal reference. 
* `ParamType` is neither pointer or reference.

## Case 1: *ParamType* is a reference or Pointer, but not Universal Reference

This is the simplest case. 

1. If `expr`'s type is a reference, ignore the reference part
2. Pattern-match `expr`'s type against `ParamType` to determine T.

```cpp
template<typename T>
void f(T& param); // param is a reference

// take these declarations
int x = 27;
const int cx = x;
const int& rx = x;

f(x);               // T is int,        param's type is int&
f(cx);              // T is const int,  param's type is const int&
f(rx);              // T is const int,  param's type is const int&
```

Passing a const object to a reference parameter, the caller expects that object to remain unmodifiable. The constness of the object becomes part of the type deduced for T. Interesting is that even when the variable passed in is a reference like in `int& rx = x;`, T is still deduced to be a non-reference. This works the same for rvalue references.

```cpp
template<typename T>
void f(const T& param);

int x = 27;
f(x);               // T is int,        param's type is const int&
```

All of this works as expected. 

## Case 2: *ParamType* is a Universal Reference

Universal reference parameters are declared like rvalue references, but they behave differently when lvalue arguments are passed in. 

* If `expr` is an lvalue, both T and `ParamType` are deduced to be lvalue refs. This is very odd because it's the only situation where T is deduced to be a reference, and because `ParamType` is deduced to an lvalue ref instead of an rvalue ref.

```cpp
template<typename T>
void f(T&& param);                      // param is now universal reference

int x = 27;
const int cx = x;
const int& rx = x;

f(x);               // x is lvalue      T is int&           param's type is int&
f(cx);              // cx is lvalue     T is const int&     param's type is const int&
f(rx);              // rx is lvalue     T is const int&     param's type is const int&
f(27);              // 27 is lvalue     T is int,           param's type is int&&
```

When universal references are being used, type deduction distinguishes between lvalue and rvalue arguments.

## Case 3: **ParamType** is neither a pointer or reference

When `ParamType` is neither pointer or reference, we have a simple pass by value - param will be a copy of whatever is passed in. It doesn't matter if the parameters being passed in are pointers or references, they will be copied into new variables. It also ignores any constness that the arguments might have.

```cpp
template<typename T>
void f(T param);

f(x);               // T and param are both int
f(cx);              // T and param are both int
f(rx);              // T and param are both int
```

Param isn't const since it is a copy. Because it is a copy this inherently guarantees that the arguments passed in will not be modified. As above, if it's a reference then templates respect that because it would otherwise modify the data being referenced. 

## Array Arguments


















