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





