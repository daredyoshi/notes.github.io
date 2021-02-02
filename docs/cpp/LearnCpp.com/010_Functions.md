# Functions

## By Value, Reference, Address and Return types

This is going to serve as a thourough review of functions. There are three ways of passing variables into functions: by value, by reference and by address. 

Passing by value makes it impossible to modify the original argument. This is a simple and safe way. Passing by value copies the arguments, which can be problematic for very large data objects.

Passing by reference does not copy the objects, which means they can be modified by the function. This can be circumvented by making the function parameters const. This can be also be useful if a function needs to return more than one argument. One convention for these is to suffix them with out, and put them at the end of the function's parameter list. These are often called **out parameters**. You should always use pass by const reference unless intentionally modifying the out parameters. 

**Passing by address** is passing the actual address into the function as a pointer. It's also a good practice to make this input pointer address a const as well if not modifying it. Theoretically these addresses are passed by value. So if you change what the pointer is pointing to in the function then afterwards the pointer will be unchanged. You can also pass addresses by reference though. 

Be careful about returning values by reference - if they are defiend in the fucntion they will go out of scope and you will be left with a dangling reference! This is only really used to return arguments passed by reference to the function back to the caller. 

Return by address is useful when returning large objects that have been dynamically allocated in the function (new). 

You can return multiple elements with out parameters or also by ```std::tuple<type, type, etc>```. 

```cpp
#include <tuple>
#include <iostream>

std::tuple<int, double> returnTuple(){
    return { 5, 6.7};
}

int main(){
    int a;
    double b;
    std::tie(a, b) = returnTuple();
    return 0;
}
```

## Inline Functions

One drawback of functions is that every time they are called, there is a very tiny amount of cpu overhead. this is because the CPU must store the adderss of the current instruction it is executing along with registers, copying the values, etc. In-place code is much faster. This isn't bad for really large functions, but for small ones that get called millions of times it can make a difference. C++ offers **inline** functions to make up for this. This bascially tells the compiler to expand all the functions inline, so that there is no longer a reference to an extra data object!

Because of code bloat, inlining function is best suited to short functions. You can force inline a function with the following keyword

```cpp
inline int min(int x, int y){
    return x> y ? y : x;
}```

Most modern compiliers automatically inline functions as appropriate. It's interesting though, because inline function can skirt the one-definition rule since they are not actually referenced by the linker. This will get interested when you get member functions in the next chapter. 

## Function overloading

**Function overloading** is a feature of C++ where you can have different amounts of arguments in functions of the same name, or different argument types. 

```cpp
in add(int x, int y);
double add(doublex, double,y);
```

The compiler is able to tell which version of add() to call based on the arguments used in the function call. Not that function return types are not considerd for uniqueness. This will not compile:

```cpp
int getRandomInt();
double getRandomDouble();
```

In trying to match the correct function the inputs C++ will try to find a match through promotion if there is no immediate matching function.

* Char, unsigned char, and short is promoted to an int.
* Unsigned shrot can be promoted to into or unsigned int, depending on the size of an int
* Float is promoted to double
* Enum is promoted to int

```cpp
void print(float value);
void print(Employeed value);

print('a'); // a is converted to match print(float)
```

Finally C++ tries to match through user-defined conversion. Classes can define conversions to other types that can be implicitly applied to objects of that class. 

Even with all this, it is still possible for an ambiguous match. These are considered a compile-time error. Just freaking cast your vars dude. 

Function overloadig can simplify you program, but it can be tricky to handle different return types. 

## Default Arguments

A **default argument** allows you to make function parameters optional. These parameters are then called **optional parameters**. This is useful for functions that are usually supposed to operate a certain way, but might want to operate a different way sometimes. 

Note that this does not allow you to change the order the arguments are input to the function. So you can't have printValues(,,3). Because of this if more than one default argument exists, the leftmost default argument should be the one most likely to be explicitly set by the user. 

You cannot re-declare default arguments if a function has been forward-declared. If it has, put the default argument into the forward declaration. 

in foo.h
```cpp
void printValues(int x, int y=10);
```
 in main.cpp
 ```cpp
 void printValues(int x, int y){
    // print the things. 
 }
 ```

You can use default arguments in combination with function overloading. It's important to note that these arguments do NOT count towards making the function unique. 

This doesn't compile

```
void printValues(int x );
void printValues(int x, int y=20);
```

This does:

```
void print(std::string string);
void print(char ch=' ');
```

The secone one basically counts as "no arguments" from the perspective of the compilier.

## Function Pointers

You can have pointers that point to functions!? This is what functions are when you use them with the (). 

```cpp
int(*fcnPtr)(){&foo};
```

For a const function pointer you need to put the * before the const, otherwise it would get interpreted that the function would return a const int. 

```cpp
int (*const fcnPtr)(){&foo};
```

Note that there's no () after foo. Otherwise we would be assigning the return value of foo to the pointer which could be valid if foo is returning a function. 

C++ *will* implicitly convert function to their address, so the & is only there to be extra explicit. Note that default parameters won't work for functions called through function pointers, since the default parameters are supplied at compile-time, and function pointers are resolved at run-time. 

One of the most common uses for this is to pass function points as arguments to other functions. Sorting algorithms are an example of this. You can re-write the selection sort from a previous lesson like this

```cpp
void selectionSort(int *array, int size, bool (*comparisonFcn)(int, int)){
    ...
    if (comparisonFcn(array[bestIdx], array[currentIndex])){
        ...
    }
}

int main(){
    ...
    selectionSort(array, 9, descending);
    ...
}
```

This prevents you from having to write multiple function for every single sort case! Often you will want to provide a default function:

```cpp
void selectionSort(int *array, int size, bool (*comparisonFcn)(int, int) = ascending);
```

Consider making your function pointer parameters prettuing using using

```cpp
using ValidateFuntion = bool(*)(int, int);
// bool validate(int x, int y, bool (*fcnPtr)(int, int)); // ugle
bool validate(int x, int y, ValidationFunction pfcn)// clean
```

You can also use std::function to store and define function pointers. 

```cpp
#include <functional>
bool validate(int x, int y, std::function<bool(int, int)> fcn);
```

You can also use auto to infer the type from a function. You should generally use std::function for all your function pointer needs because it's much cleaner. 

## Stack and heap

The memory that a program uses has several segments (areas):

* The code segment: readonly segment of the raw text of the code in memory.
* The bss segment: uninitialized data segment where zero-intialized global and static variables are stored
* the data segment: initialized data segment where initialized global and static variables are stored
* the heap - where dynamically allocated variables are allocated to
* the call stack, where function parameters, local variables and other in-brackets, limited-scope information is stored. 

The most interesting stuff happens in the heap and stack. We already talked about the heap in [ch9](../009_Arrays_Strings_Pointers_and_References), and how you dynamically allocate memory to it with new and delete. There is no guarantee that this allocated memory is sequential - this is a process that "just works". 

The heap is

* comparatively slow to allocate
* manual allocation/cleanup
* memory must be accessed through pointer. Dereferencing this is slower than accessing memory directly.
* It's a huge pool, and so can store very large objects.

The **call stack** is what keeps track of all the active functions, and all auto allocation of function parameters and local variables. 

#### What is a stack data structure?

In order for data to be used efficiently, **data structures** are created to organize and sort it. Arrays and structs are data structuers, and they provide mechanism for storing data and accessing that data in an effiecient way. A stack is more limited than an array which can access any element at any time (**random access**). In the stack what you can do is

1. Look at the top item on the stack: top() or peek()
2. Take the top item off the stack pop()
3. Put a new item on the top of the stack push()

Stack is a last-in, first-out (LIFO) structure. The last item pushed onto the stack is the first one taken off. 

The call stack segment holds the memory used for the call stack. Whenever a function is encountered, the function is pushed on the call stack, and when it ends it is removed from the call stack. Every item pushed to the call stack is called a **stack frame**. You can imagine a marker on a giant stack of mailboxes. When you add new mail, you move it up to the last filled mailbox. Then when mail is "delted" you move it down, except you don't empty the redundant mailboxes. You just fill them again next time. 

The call stack 

1. The program encoutners a function call
2. Stack frame is constructed and pushed on the stack. This frame consists of:
    * The address of the instruction (**return address**)
    * All the function arguments
    * Memory for local variables
    * copies of registers modified by the function that need to be restored when the function returns
3. The Cpu jumps to the functions's start poitn
4. The instructions inside of the function begin.

After the function terminates

1. Registers are restored from the call stack
2. The stack frame is popped off the stack freeing all local variables and arguments.
3. The return value is handles
4. The CPU resumes execution from the return address. 

This is the basic rundown of the call stack. If a program tries to put too much information on the stack, **stack overflow** will result. It's generally the result of allocating too many variables on the stack and/or too many nested function calls. 

* Allocating memory on the stack is fast
* Memory on the stack stays in scope as long as it is on the stack and popped off after
* All memory on the stack is known at compile time and can be accessed directly through a variable
* The stack can't handle large amounts of memory. 

std::vector is a great alternative for large arrays because it interally hanles all the allocation to the heap. Even better is that if you make a vector smaller, it keeps it's larger capacity. It doesn't do intensive resizeing unless necessary. If you know you will be pushing a lot onto the vector like with ```array.push_back()```, it can be a good idea to ```array.reserve(5)``` memory given the case that you know you will be pushing 5 variables to the vector. 

## Recursion

A **recursive function** is a function that calls itself. If a recursive function goes on for infinity at some point the program will terminate because the stack is out of memory! You always want some sort of termination condition (like an if statement) to stop recursive function from going on for infinity. This is a pretty neat example

```cpp
int fibonacci(int count)
{
    if (count == 0)
        return 0; // base case (termination condition)
    if (count == 1)
        return 1; // base case (termination condition)
    return fibonacci(count-1) + fibonacci(count-2);
}
 
// And a main program to display the first 13 Fibonacci numbers
int main()
{
    for (int count=0; count < 13; ++count)
        std:: cout << fibonacci(count) << " ";
 
    return 0;
}
```

This isn't very efficient, because fibonacci is called twice for each step in recursion! It can be optimized with a technique called **memoization** caches the results of expensive function calls so the result can be returned when the same input occurs again. 

```
// h/t to potterman28wxcv for a variant of this code
int fibonacci(int count)
{
	// We'll use a static std::vector to cache calculated results
	static std::vector<int> results{ 0, 1 };

	// If we've already seen this count, then use the cache'd result
	if (count < static_cast<int>(std::size(results)))
		return results[count];
	else
	{
		// Otherwise calculate the new result and add it
		results.push_back(fibonacci(count - 1) + fibonacci(count - 2));
		return results[count];
	}
}

// And a main program to display the first 13 Fibonacci numbers
int main()
{
	for (int count = 0; count < 13; ++count)
		std::cout << fibonacci(count) << " ";

	return 0;
}
```

This caches all the results of the fibbonachi numbers that have already been calculated. Note that theoretically all recursion examples can also be solved iteratively (*for*, and *while*). This is also generally more efficient, since it doesn't include the overhead of a function call. Recursion is a good choice when

* The recursion code is simpler to implement
* The recursion depth < 100,000 levels at all times
* The iterative version of the algorithm requires managing a stack of data
* This isn't a performance-critical section of code.

## Command line arguments

**Command line arguments** provide a way for other programs to input data into your program. To add command line arguments, you use them as an input to your main as follows:

```cpp
int main(int argc, char argv{})```

* **argc** is an integer parameter containing a count of the number of arguments passed to the program. (**arg**uments **c**ount)
* **argv** is where the values are stored (**arg**ument **v**alues). It's an array of C-style strings.

Here is an example for getting an integer input

```cpp
// h/t to potterman28wxcv for a variant of this code
int fibonacci(int count)
{
	// We'll use a static std::vector to cache calculated results
	static std::vector<int> results{ 0, 1 };

	// If we've already seen this count, then use the cache'd result
	if (count < static_cast<int>(std::size(results)))
		return results[count];
	else
	{
		// Otherwise calculate the new result and add it
		results.push_back(fibonacci(count - 1) + fibonacci(count - 2));
		return results[count];
	}
}

// And a main program to display the first 13 Fibonacci numbers
int main()
{
	for (int count = 0; count < 13; ++count)
		std::cout << fibonacci(count) << " ";

	return 0;
}
```

## Ellipsis

Don't use ellipsis. (...) captuer optional arguments. It can be thought of as an array that holds extra arguments. It allows functions to take a varialbe number of arguments without having to make an overloaded variation for each one. What makes them dangerous is that type checking is suspended. 












