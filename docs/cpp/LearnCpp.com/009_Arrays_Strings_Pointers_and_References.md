# Arrays, Strings, Pointers, and references

An *array* is a aggregate datat types that lets us access many variables of the same type though a single identifier. They are declared like this:
```cp
int testScore[30]{};// allocate 30 integer variables in a fixed array
```

The 30 in brackets tells the compiler both that this is an array variables as well as the **array length** - the amount of variables to allocate. A **fixed array** is an array where the length is known at compile time. It cannot chaneg length. Each of the variables in an array is called an **element**. They do not have unique identifiers, but are accessed using the **subscript operator ([])** and a parameter called the **subscript** or **index** that tells the compilier which element we want. This is called **subscripting** or **indexing** the array. The **range** of an array is the numbering of the elements in the array. 

Arrays can be made from any data types, including structs. To access struct members:

```cpp
rects[0].length = 24
```

Array subscripts must always be an integral type. These can be char, short, int, long, long long, enums, etc. For fixed array declarations you need the length to be a compile time const - it cannot be calculated dynamically at runtime. 

You can also initialize arrays with an **initizlizer list**:
```cpp
int prime[5]{2, 3, 5, 7, 11};
```
If you are initializing with an initializer list you don't need to specify the length of the array. 

If there are less initilizer in the list than the array can hold, the remaining elements are initialized to 0 - caled **zero initialization**. Even if you are find with all the elements being initialized to 0, you should still initialize them with empty brackets

```cpp
std::string array[5]{ };
```

For fixed arrays you can have better code documentation by using enums. 

```cpp
enum StudentNames
{
    kenny, // 0
    kyle, // 1
    stan, // 2
    butters, // 3
    cartman, // 4
    max_students // 5
};

int testScores[max_students]{};
testScores[stan] = 76;
```

When passing arrays to a function, the entire array is not copied. Only the *actual* array is copied, but not the elements. This means that all elements are passed **by reference** - they can be changed by the function!!To signify that a function will not change the elements of an array you can make the array const. 

Keep in mind that Cpp will not stop you from assigning out of range elements to arrays! That value will simply overwrite that next part of memory, which could be allocated to a different variable!

The std::size can measure the size of an array. Note that due to how the arrays are passed into functions (as pointers) this will not work on arrays in functions! Also note that std::size returns an unsigned variable. 

For all cases where this is required, you can use a **dynamic array*, whose length can be set at runtime. 

You can **iterate** over the values in an array using a loop to apply the same thing to every entry.

```cpp
int scores[]{ 84, 92, 76, 81, 56 };
int numStudents{ std::size(scores) };
// const int numStudents{ sizeof(scores) / sizeof(scores[0]) }; // use this instead if not C++17 capable
int totalScore{ 0 };

// use a loop to calculate totalScore
for (int student{ 0 }; student < numStudents; ++student)
    totalScore += scores[student];

auto averageScore{ static_cast<double>(totalScore) / numStudents };
```

Use std::swap to swap two elements of an array. This is useful when sorting arrays. 

For this lesson the assignment was to write a [bubble sort](https://github.com/daredyoshi/learn_cpp/tree/master/bubble_sort) program to sort an array. 

Arrays can also be arrays of arrays called **multidimensional arrays**. When thinking of these like a matrix, it's convenient o think of the first (left) subscript as being the row, and the second (right) subscript as being the column. Interpreting multi-dimensional arrays this way is considered **row-major** order. 

```cpp
array[3][5];
```

You can initiliaze these arrays with nested braces:

```cpp
int array[3][5]{
    { 1, 2, 3, 4, 5 },
    { 6, 7, 8, 9, 10 },
    { 11, 12, 13, 14, 15 }
};
```

## C-style strings

You will never use these, but it's interesting to know that std::string are based on **C-style strings** which are **null terminated** strings. This means that a **null terminator** '\0' is used to indicate the end of the string. These strings are essentially arrays of chars, and are defined that way:

```cpp
char myString[]{ "string" };
```

If you conver the string chars to ints, the last will always be zero. Because these strings work exactly like arrays, you can't assigne values of them directly! It's fine if the array is larger than the string it contains, but if you replace the last element in it and print, the print will keep printing until it encounters a zero!

You should use std::stirng or std::string_view instead of C-syle strings. 


## String View

This is an object which, like a pointer, views an existing string object. Prefer this over C-style strings and std::string for read-only strings. It includes function for reducing what parts of the string it referrs to. For this you use std::strnigview.remove_prefix and remove_suffix. It's also not null-terminated. If the string that std::string_view is viewing goes out of scope or becomes unavaliable, then it's uncertain what string_view is pointing to. You can convert it to a std::string by casting it to one. 

## Pointers

The **address-of operator (&)** allows us to see what memeory address is assigned to a variable, and the **indirection (*)** operator allows us to access that value. 

```cpp
int x{ 5 };
std::cout << x << '\n'; // print the value of variable x
std::cout << &x << '\n'; // print the memory address of variable x
std::cout << *(&x) << '\n'; /// print the value at the memory address of variable x (parenthesis not required, but make it easier to read)
```

A **pointer** is a variable that holds a memory address as its value. 
```cpp
int *iPtr{};
```

The asteric is not an indiretion - it's just part of the pointer declaration syntax. When defining variables it's bettr to put the asterisk next to the variable name, but when returning a pointer from a function it's better to put the asterisk of a pointer return value next to the type.

```cpp
int* doSomething();
```

One of the most commoon things to do with pointers is to have them hold the address of a different variable. 

```cpp
int v{ 5 };
int *ptr{ &v }; \\inititialize ptr with address of variable v
```

The type of the pointer has to match the type of the variable being pointed to. It's worth noting that the address of (&) doesn't return the addres of its operand as a literal, but returns a pointer containing the address of the operand. 

The other common thing to do with pointers is to get the values that they are pointing at. For this use the indirection (*) operator to evaluate the cnotents of the address it is pointing to. 

```cpp
int value{ 5 };
int *ptr{ &value }; // ptr point to value
std::cout << *ptr; // print the value that the ptr is pointing to 
```

This is why pointers need a type. Without a type, when indirecting throught a pointer, the pointer wouldn't know how to interpret the contents it was poitning to. 

You can reassign pointers to different values of the same type. If your program tries to access a location in memory that's not part of the program, the operating system might crash. Because of this all operating systems sandbox applications. 

What are pointers good for?

1. Arrays are implemented using pointers
2. They are the only way you can dynamically allocate memory in C++
3. They can be used to pass a function as a parameter to another function
4. They can be used to achieve polymorphism when dealing with inheritance. 
5. They can be used to have one struct/class point to another struct/class to form a chain. 

What should you initialize a pointer to if you don't have the memory address yet? The answer is a **null value**, which is a spacial value that means the pointer is not pointing at anything. Pointers pointing at null values are called **null pointers**. They are assigned by initializing the pointer to 0 or (C++ 11) a special value called null ptr. 

```cpp
float *ptr { nullptr };
```

Your pointers should always be initializaed even if it is to null value. Some compilers misinterpret the value of zero and try to initialize pointers to that as an integer, which is why nullptr was added. 

Pointers and arrays are intrinsicalyl related in C++. When you pass an array to a function, it **decays** (is implicitly converted) to a pointer that points to the first element of the array. Because both pointers and arrays can access elements in them with the subscript, it might be better to understand where the data types differ:

1. sizeof() on arrays returns the entire array (array length * element size). On a pointer it returns the size of a memory address. 
2. Using the address-of operator (&) on a pointer yields the memory address of the opinter variables. Of an array it returns a pointer to the entire array. The resulting pointer points to the same address, but the data type is different. 

Arrays degrade to pointers because they have all of the same funcionality and a much smaller memory footprint. They do not decay if they are part of a class or struct. 

When **scaling** a pointer, you are telling it to point to the next object in memory of the pointer's type. This assumes that the next object is the same size as the pointer, and the same type. How is this useful? It's because arrays are laid out sequentially in memory. This is how you grab a specific element in an array. 

These are equal:

```cpp
array[1];
*(array+1);
```

## Dynamic Memory Allocation with new and delete

C++ supports three basic types of memory allocation:

1. **Static memory allocation** for static and global variables. This is avaliable for the entire runtime of the program. 
2. **Automatic memory allocation** for function parameters and local variables. Memory for these types is allocated when the relevant block is entered, and free when the block is exited. 
3. **Dynamic memory allocation** - which is user controlled. 

Why not just use static and automatic?

1. Wasted Memory
2. Hard to tell which bits of memory are actually being used. 
3. Most variables are allocated to a portion of memory called the **stack**, which is usually quite small. This is isually around 1MB, otherwise the operating system will close the program. 
4. It can lead to artificial limitation and array overflows. What if the user has a name longer than the allocated char[25] array?

This is where **dynamic memory allocation** comes in. It is a way for running programs to request memory from the operating system when needed. It comes from a much larger pool memory managed by the operating system called the **heap**. On modern machines this is gigabytes in size. 

To allocate a *single* variable dynamically we use the scalar (non-array) form of the **new** operator. 

```cpp
int *ptr {new int}; // dynamically allocate an integer (and discard the result)
```

new int returns a pointer that we are storing in our automatically allocated ptr variable. We can then perform indirection through the pointer to access the memory 

```cpp
*ptr = 7; //assign value of 7 to allocated memory
```

When you dynamically allocate memory, you're asking the operating system to reserve some of thatmemory for your program's use. You can also directly initilize the variable you are directly allocating:

```cpp
int *ptr1{ new int { 6 }}
```

To release the variables you use the **delete** operator. You should only delete ptrs that are not pointing to dynamically allocated memeory. A pointer that is pointing to deallocated memory is caleld a **dangling pointer**. After deleting a pointer you should assign it to a nullptr immediately afterward. 

```cpp
int *ptr{ new int{} }; // dynamically allocate an integer
int *otherPtr{ ptr }; // otherPtr is now pointed at that same memory location

delete ptr; // return the memory to the operating system.  ptr and otherPtr are now dangling pointers.
ptr = nullptr; // ptr is now a nullptr

// however, otherPtr is still a dangling pointer!

return 0;
```

Operator new can fail if the operating system has no more memory to grant the request with. If this happens, the program will simply terminate with an unhandled exception error. There is an alternate form of new that will return a null pointer if memory can't be allocated. 

```cpp
int *value = new (std::nothrow) int; \\ the value will be a null pointer if the integer allocation fails. 
```

At that point you probably want to test if the pointer is null and throw an exception otherwise to elegantly exit the progam. 

Because a pointer can go out of scope without freeing the memory it was pointing to, there can be instances where it is impossible to delete the allocated memory. This is called a **memory leak**. This can also happen if the pointer is assigned to another value, in which case you should have deleted the memory the pointer was pointing towards before re-assigning it. 

Allocate memory for an array and return a pointer:

```cpp
int *array{ new int[length]{} };
delete[] array;
array = nullptr;
```

You cannot have a non-const pointer to a const value. You can have const pointers to non-const values, but not non-const pointers to const values. You can have a const int *ptr to a value and the pointer will think the value is const even if it isn't. 

```cpp
int value = 5;
int *const ptr = &value;
```

A **const pointer** is a pointer whose value can not be changed after initialization. The value that this pointer points to cannot be changed. You can also have a const pointer pointing to const int type like this

```cpp
int value = 5;
const int *const ptr = &value;
```

* A non-const opinter can be redirected to point to other addresses
* A const pointer always oint to the same address, and this address can not be changed
* A pointer to a non-const value can change the value it is pointing to. These pointers can not point to a const value. 
* A pointer to a const value treats the value as const (even it is not), and thus cannot change the value it is pointing to. 

## Reference Variables

**Reference variables** can be thought of like instances of a variable, it acts like an alias to another object or value. This is different that pointers that point to the memory address of an object or value, or normal variables, which hold values directly.

```cpp
int value { 5 };
int& ref1{ value };
int &ref2{ value };
```

The second initiliazation form is preferred for variables, and the first for function return types, just like with the address of and indirection operators. References must be initialized when created and won't compile otherwise. They cannot be re-assigned trying to do so will re-assign the value to the reference. They are most often used as function parameters. This is especially useful when the function is intended to modify the variable, and is what people means when they say **"pass by reference"**. 

All varialbes are a type of **l-value** (ell-value), which are values that have an address in memory. These are the only values that are on the left side of an assignment statement. The opposite of l-values are **r-valuse** - the are expressions that are not l-values. 

```cpp
x = x + 1
```

In the above statement x is both used as an l-value and an r-value. The key takewaway is that the right side you have something that will be evaluated and then put into the variable's address in memory on the left side. 

You can have const references just like const pointers. Unlike pointers you can have const references to non-const values. This is great if you want to illustrate that this reference variable won't change. A great use of const references is as function parameters:

```cpp
void changeN(const int& ref){
    ref = 6; //not allowed, ref is const. 
}
```

When passing C-style arrays to a function, if they are passed as reference they do not decay. Their size needs to be defined in the parameter though. References are particularly useful when used as an alias for deeply nested objects. 

```cpp
int &ref{ other.somthing.value1  };
```

References are generally much safer than pointers, but have more limited functionality. Pointers should only be used in situations where references are not sufficient (such as dynamically allocating memory). For references the compilier automatically add the indirection, which we'd do manually on a pointer using an asterisk. Pass non-pointer, non-fundamental data type variables (such as structs) by (const) reference, unless you konw that passing it by value is faster. 

Structs are generally passed around by reference or pointer
```cpp
struct Person{
    int age{};
    double weight{};
};

Person person{};
Person &ref{ person };
Person *ptr{ &person };
ptr->age = 5; \\ remember that -> is the same as (*ptr).age = 5;
```

Remember that the * does the same as indirection followed by the . member selection. When using a pointer to access teh value of a member, use the operator -> instead of the (.)

## For-each loops

For most loops, it's simpler and safer to use a **for-each** loop instead of manual for loops. The syntax is as follows:

```
for (element_declaration : array)
    statement;
```

For each loops are a great place to use the auto keyword. This saves a lot of time when refactoring the contents of a list. Because the list is alerady strongly typed, the auto is not ambiguous. 

```
for(auto number : fibonacci){ \\ type is auto, so number has its type deducted from the fibonacci arary
    std::cout << number << ' ';
}
```

The above example copies the value into number from fibonacci. In most cases it's more efficient to do this by reference:

```cpp
for(autch &number: array){
    std::cout << number << ' ';
}```

In for-each loops element declarations, if your elements are non-fundamental types, use references or const references for performance reasons. As of cpp 20, you can auto assigne a variable with the current index of the for loop:

```cpp
for (int i{ 0 }; auto score : scores){
    std::cout << score << ' ';
}
```

## Void Pointers

Similar to the auto keyword, the **void pointer** can point at any datatype. This is great, but indirection will not work because the void pointer doesn't know how large the data type is. 

```cpp
int value{ 5 };
void *voidPtr{ & value };

std::cout << *voidPtr << '\n' // this will not compile
int *intPtr{ static_case<int*>(voidPtr) }; // you need to cast to int first
std::cout << *intPtr << '\n';
```

The difficulty with void points is that you need to keep track of what their data type is. 

## Xtra juice

Interesting but rarely used knowlage: you can have pointers to pointers:

```cpp
int **ptrptr; \\ pointer to a pointer to an int, two asterisks
```

The most common use for this is to dynamically allocate an array of pointers. This is like a standard dynamically allocated array, except the array elemeents are of type "pointer to integer" instead of integer. This is espcially useful for multidimensional arrays of pointers. 

```cpp
int **array = new int*[10]; \\ allocate an array of 10 int pointers - these are our rows
for (int count = 0;  count < 10 ++count){
    array[cout] = new int [5]; \\ these are our columns.
}

\\deallocating the the array also requires a loop
for (int count = 0; count < 10; ++count)
    delete[] array[count];
delete[] array; // this needs to be done last
```

We need to deallocate the array elements first, then the array. Otherwise there would be no array left to access the elements that we are deleting. 

Instead of complex multidimensional arrays consider a flat array where you access the elements by the (row * numberOfColumnsInArray) + col;

Generally avoid using pointers to pointers unless no other options are available.


## std::array

This object provides fixed array functionality and does not decay into a pointer when passed to a function. 

```cpp
std::array<int, 5> myArray {9, 6, 7, 2, 4, 5, 2, 1};
```

One good thing about std::array is using the myArray.at(1) function to acces elements. This will throw an error if out of range unlike the [] operators. It will also clean up after itself when out of scope. Because it is not a fundamental data type you should alsoway pass std::array by reference or const reference. 

You can sort std::array using std::sort which lives in the <algorithm> header.

```cpp
std::sort(myArray.begin(), myArray.end());
```

std::array also come with a .size() function which is really nice when looping over the elements. It's a bit weird through, because .size() return an unsigned integral data type defined inside of std::array so when looping over it you have to do it like this:

```cpp
std::array myArray { 7, 3, 1, 9, 5 };

for (std::array<int, 5>::size_type i{ 0 }; i < myArray.size(); ++i){
    std::cout << myArray[i] << ' ';
}
```

A better solution is to just avoid manual indexing of std::array and use a range-based for loop

This still makes it a challenge to iterate over the array in reverse because size is an unsigned int. For this modify i each iteration and see if that it still above zero to prevent i from ever reaching zero and then looping back up to the maximum value. 

```cpp
for(auto i{ myArray.size() }; i-- >0; ){
    std::cout << myArray[i] << ' ';
}
```

When initializing an array of structs or nested array, it can be confusing with the braces. The first element in the initialization is the key argument, and arrays of arrays must be defined within in:

```cpp
Array houses{
    { {13, 4, 30}, {14, 3, 10}, {15, 3, 40} }
}
```

Those extra braces are imporant! std::array is a great replacement for the build build-in fixed array, but with a slightly awkward syntax. 

## std::vector

The true dynamic list obect! This is what you use to work with dynamic arrays. It's one of the most useful and versatile tools. With std::vector you can create arrays that have their length set at runtime without having to explicitly allocate and deallocate memory using new and delete. It lives in the ```<vector>``` headers. 

Declaring it is simple:

```cpp
std::vector<int> myArray { 9, 7, 8, 9, 1, 2, 3 };
```

Note that you do not need to include the array length at compile time. std::vector will dynamically allocate. Because it cleans itself up when going out of scope you do not need to worry about memeory leaks. Vectors also remember their length. Resizing a std::vector is as simple as calling the resize() function.

```cpp
std::vector array { 0, 1, 2 };
array.resize(5);
```

This is still computationally expensive, so you should still try to minimize the cases where you resize vectors. You should use std::vector in most cases where dynamic arrays are needed. 

## Iterators

How do range-based for loops work? An **iterator** is an object designed to traverse through a container while providing access to each element while it's doing so. Pointers are the simplest kind of iterator. Because a pointer knows the size of memory to increment, you can use it to iterate over a block of memory. 

```cpp
std::array data { 0, 1, 2, 3, 4, 5, 6 };
auto begin { &data[0] }; \\ create a pointer that points to the first element
auto end { begin + std::size(data) }; \\ this points to one spot beyond the last element. The spot where in the for loop the ptr will stop iterating. 

for(ato ptr{ begin }; ptr!=end ++ptr){
    std::cout << *ptr << ' ';
}
```

Because iterating is so common, the array objct has convenience function named begin and end

```cpp
auto begin { array.begin() };
auto end { array.end()};
```

If it's not an array you can also use std::begin(array) and std::end(array). Any type that has a begin and end function or can be used with std::begin and std::end are usable directly in range-based-for loops (this is what they look for). 

```
for (int i: array) \\ array has array.begin() and array.end()
```

Behind the scenes begin() and end() are called. Dynamic arrays won't work though, because there is no end() function. 

If the elements being iterated over change address or are destroyed iterator can be left dangling: then they are reffered to as **invalidated**. Accessing invalidated iterators produces undefined behaviour. 

## Standard Library algorithms

Don't type up your own complex for loops for things you can easily do with std. These are all found in the ```<algorithms>``` [library](https://en.cppreference.com/w/cpp/algorithm). 

* **std::find** searched for the first occurrence of a value. ```std::find{arr.begin(), arr.end(), search)}```
* **std::find_if** 
```
bool containsNut(std::string_view str){
    // std::string_view::find returns std::string_view::npos if it doesn't find the substring. Otherwise it returns the index where the sustring occurs in str. 
    return (str.find("nut") != std::string_view::npos);
}

int main(){
    auto found{ std::find_if(arr.begin(), arr.end(), containsNut) };
}
```

This return all elements of the array if they contain nut (I did abbreviate this for berevity). The string view object has a function find which returns a bool. That is used on each element in the array until one is found. 

* **std::count** and **std::count_if** see how many occurances in an iterator match the comparison. 

* **std::sort** for custom sorting. This takes in a function very similar to std::find_if. ```std::sort(arr.begin(), arr.end(), std::greater<int>{})``` - greater is also part of std

* **std::for_each** with a function applies that to every element of the list:

```cpp
void doubleNumber(int &i){
    i*=2;
}

int main(){
    std::for_each(arr.begin(), arr.end(), doubleNumber);
}
```

The advantage here is that you easile set the beginning and end. 

* **std::reduce** applies a function to all elements in a list (by default the + operator), which results in a single value. By default (+), this is the sum of all the elements of the list. 

* **std::accumulate** - this does the same as std::reduce, but from left-toright

* **std::shuffle** takes a list and randomly re-orders its elements.

Unless otherwise specified, do not assume that the standard ilbrary algorithms will execute in a particular sequence. This means they do not iterate over the for-loop in sequential order, but in parallel. Favor these functions over writing your own. 

A game I wrote with the concepts of this chapter can be found [here](https://github.com/daredyoshi/learn_cpp/blob/master/card_game/src/CardGame.cpp)






