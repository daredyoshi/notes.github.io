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

### Case 1: *ParamType* is a reference or Pointer, but not Universal Reference

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

### Case 2: *ParamType* is a Universal Reference

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

### Case 3: **ParamType** is neither a pointer or reference

When `ParamType` is neither pointer or reference, we have a simple pass by value - param will be a copy of whatever is passed in. It doesn't matter if the parameters being passed in are pointers or references, they will be copied into new variables. It also ignores any constness that the arguments might have.

```cpp
template<typename T>
void f(T param);

f(x);               // T and param are both int
f(cx);              // T and param are both int
f(rx);              // T and param are both int
```

Param isn't const since it is a copy. Because it is a copy this inherently guarantees that the arguments passed in will not be modified. As above, if it's a reference then templates respect that because it would otherwise modify the data being referenced. 

### Array Arguments

Array types are different from pointer types for templates just like for C - the array **decays** into a pointer. When an array is passed into a template parameter, it's treated as a pointer declaration.

```cpp
void myFunc(int param[])
void myFunc(int* param); // this is equivalent to the above
```

While arrays cannot be passed in directly without decaying to pointers, they can be passed by reference.

```cpp
tempalte<typename T>
void f(T& param);

char[13] name = "adkfksofydnecx";
f(name) // this is passed in as an array including the size
// the type of param becomes const char (&)[13]
```

With this it is possible to decude the size of an array at compiletime using templates

**Find size of array at compiletime**

```cpp
// return size of an array as a compile-time constant. (The
// array parameter has no name, because we care only about
// the number of elements it contains.)
template<typename &, std::size_t N>
constexpr std::size_t arraySize(Tb (&)[N]) no except{
return N;
}

// Now we can, for example, declare an array with the same
// number of elements as another one
int keyvals[] = { 1, 2, 3, 4, 67, 43, 2, 13 };
int mappedVals[arraySize(keyvals)];
```

Of course the is useless to a modern cpp developer who would just  use ```std::array```

### Function Arguments

Just like arrays, function types can also decay into function pointers. All of the above applies to that just same.

```cpp
void someFunc(int, double);         // someFunc is a function;
                                    // type is void(int, duoble)

template<typename T>
void f1(T param);                   // in f1, param passed by value

template<typename T>
void f2(T& param)                   // in f2, param passed by ref

f1(someFunc);                       // param decuded as ptr-to-func;
                                    // type is void (*)(int, double)

f2(someFunc);                       // param decuded as ref-to-func;
                                    // type is void (&)(int, double)
```

Doesn't really make much difference in practice, but interesting to know?? 

Template type dection is what ```auto``` type deduction is built on top of. The special treatment of universal references and the decay to pointers are ones to watch out for. If you are unsure of what types are getting deduced check out **TODO add chapter 4 here when I've gotten notes to it**

!!! NOTE
    * Templates deduce reference arguments as non-references
    * lvalue arguments for universal reference parameters get special treatment
    * by-value parameters that are ```const``` and ```volatile``` are treated as non-```const``` and non-```volatile```
    * Arguments that are array or function names decay to pointers unless they're used to intialize references.

## Item 2: Understand ```auto``` type deduction

```auto``` type deduction *is* template type deduction. 

It's literally the same algorithm to deduce the types. When a variables is declared usin ```auto```, ```auto``` plays the role of T in the template, and the type specifier for the variable acts as ```ParamType```. 


```cpp
auto x = 27;        
const auto cx = x;
const auto& rx = s;

// these types are decuded the same as the following

tempalte<typename T>
void func_for_x(T para);

funx_for_x(27);

template<typename T>
void func_for_cx(const T param);

func_for_cx(x);

template<typename T>
void func_for_rx(const T& param);

func_for_rx(x);
```

There is only one difference between ```auto``` and template type deduction.

There are now 4 ways to initialize ints

```cpp
int x1 = 27;
int x2(27);
int x3 = {27};
int x4{27};
```

What if we want to declaer these as auto?

```cpp
auto x1 = 27;       // type is int, value is 27
auto x2(27);        // same
auto x3 = {27};     // type is std::initializer_list<int>, value is 27
auto x4{27};        // also std::initializer_list<int>
```

This is because of a specialtype deduction rule for ```auto``` if using uniform initialization the type is deduced to be ain initilizer list. If you pass the same thing into a template you get a type deduction error

```cpp
auto x = { 11, 23, 9 };

template<typename T>
void f(T param);

f({ 11, 23, 9 });               // error! can't deduce type

template<typename T>
void f(std::initilizer_list<T> initlist);

f({ 11, 23, 9 });               // T deduced as int
```

Basically the difference is that ```auto``` *assumes* that braced initializer represents a ```std::initilizer_list```, but template type deduction doesn't. 

**BUT WHY**

Scott has not found a convincing explanation for why this is. 

In C++14 things become even more interesting. Now the return type of a function can be ```auto```, but for this *template type deduction* is used NOT ```auto``` type deduction. 


```cpp
auto createInitList(){
    return { 1, 2, 3 };             // error! can't deduce type
}
```

!! NOTE
    * ```auto``` type deduduction is the same as template type deduction except when using uniform initialization where auto assumes it represents a ```std::initializer_list```
    * ```auto``` in a function return type or lambda implies template type deduction not ```auto``` type deduction
















