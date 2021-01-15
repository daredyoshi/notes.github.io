These are a dump from some google doc notes I was working on and I'm not sure if I will be formatting them, as I've decided to work through [learncpp.com](./LearnCpp.com/Introduction.md) instead.


# Introduction
C++ is a strongly typed language = each object has a predefined data type
State = what the object stores
Operations = it’s behaviour
Variable = Named object

Declare variable with = 
int the_answer=42;

### Functions
take in parameters or arguments
Return output objects to their callers
printf("Ten %du, Twenty %dv, Thirty %dw", 10x, 20y, 30z);
Must be defined before called by the compiler

Signed: positive, negative, or zero
Unsigned: non-negative

Int = signed and int by default (watch the type in the format specifier for printf)

Literal = hardcoded value in a program

### Floats
 Precision = how much memory it takes up and how detailed the value is.
Float = single precision
Double = double precision
Long double = extended precision

(generally just use a double)

### Floating point Literals
Float a = 0.1F;
Double b = 1.2;
 Long double c = 0.3L;

You can use scientific notation in literals:
Double plancks_constant = 6.62607004e-34 (no spaces)

## Character Types
Character = a single keyboard input

Char = 1 byte
char16_t = utf-16
char32_2 = utf-32
Wchar_t = large enough to contain the largest character of the implementation locale

Character literal = single, constant character. 


### Boolean
Only one type with 2 states
Ints can be bools interchangeably.
AND (&&) OR (||) are binary

Std::byte = “stood byte”
Sizeof returns a std:size_t object

void = function doesn’t return anything

### Arrays
Define them like in vex
sizeof(array) / sizeof(short)

### For loop
for(init-statement; conditional; iteration-statement){}

It understands how to iterate over each value of an array type
for(element-type element-name : array-name){}

### String Literals
Use quotes and supports unicode.

## User-Defined Types

### Enumeration Types
The simplest of the user-defined types. The values that an enumeration can take are restricted to a set of possible values. Enumeration are excellent for modeling categorical concepts. 
Basically enums

Switch statement: transfers control depending on the value of a condition. 
switch(condition){
	case(case-a):{}
	case(case-b):{}
	default:{}
}

### POD Classes
Fully features types that give flexibility to pair data and functions. Classes that only contain data are data classes
Simple containers.
Struct Book{
	Char name[256];
	Int year;
}
Book neuromancer;
Neuromancer.pages = 271

### Union Types
Dangerous and easy to misuse. 

### Fully Featured C++ Classes
Encapsulation = design pattern that binds data with the functions that manipulate it.
Put related code in one place. 
Information hiding = Hide some of the code from the rest of your program  

Access controls = public and private. Only the class can access it’s private members.
Class invariant: a feature of a class that is always true (it never varies)

### Constructors
Special methods with special declarations
Basically make a method that is called the class name. 
Braced initialization=uniform initialization
Always init with int = {} (always 0)
Int array_1[]{1, 2, 3}

You can have multiple constructors that react to different input types

### Destructor (optional)
Call this when the object is destroyed
Struct Earth {
	Earth() { // earths destructor
	printf(“Making way for hyperspace bypass”)
	 }
}

### Reference Types
These store the memory addresses of objects. 
Pointers = fundamental mechanism used to refer to memory addresses
int* my_ptr;
Dereferency by *my_ptr
This will write to that address like so
*my_ptr = 34532
Otherwise it will reference the value of the address
my_func(*my_ptr)

### -> Member-of-Pointer Operator
Dereferences (traverses) pointer
Accesses member of that pointed-to-object
Same as (*my_ptr).function();

### Points and Arrays
Creating a pointer pointing at an array will only point at the first value of that array
You can access elements with brackets just like with arrays though
Do not try to access out-of-bounds elements!!
And NEVER assign to out of bounds memory!!
You access and treat arrays and pointsers almost exactly the same.
You can also dereference and array

You can assign the fourth element like this as well *(array + 4) = val
Note that adding to the array decays it into a pointer to that element

### Void pointers and std:byte Pointers
They don’t care about the type that they are pointing to.
You cannot dereference a void*
Void arithmetic is prohibited

### nullptr and Boolen Expressions
Can use this to indicate that something failed
Any value that is not nullptr = true, nullptr = false
Function can return pointer to an object and nullptr if failed

### References&
Safer and more convenient version of pointers
They cannot be null, or researted (reassigned)
If you try to assign them a new value the new value gets assigned to the ref value

### Forward-Linked Lists:
Simple data structure made up of a series of elements.
Each element holds a pointer to the next element.
Last element holds nullptr
Elements can be discontinuous in memory
Basically lists that you can dynamically change the length of.

### this Pointers
Access the current object
Bit more verbose than just accessing the variables

### const Correctness
Means this value will not be modified.
Use to specify that a reference or pointer cannot be changed in a function.
const methods are read only
A const argument can only call const methods of itself

### Member Initializer Lists
The only way to init const values
class Thing{ Thing(const char* name) : name { name }{}}

### auto Type Deduction
Instead of spelling out the class type of a variable
auto year{2020} //int
auto& year{2020} // int& (reference)
auto* year{2020} //int* (pointer)
Great for for loops that might have their type changed.

## Object Life Cycle
Allocation = reserving space for objecting
Deallocation = releasing that storage
Automatic object = allocated at the beginning of a code block and deallocated at the end
Function parameters, anything inside that function, all local variables
Static object = global
They are deallocated when the program stops.
Extern = global outside the translation unit
local static variable = local but doesn’t get cleaned up. Variable keeps value after first initialization call of function

### Static members 
= members of a class that aren’t associated with a particular instance of the class. Changing them changes them for all instances of the class that are active
Refer to them using the scope resolution operator ::
Must be initialized at global scope unless they are const
Thread-Local Storage Duration
Thread of execution = sequence of instructions that a thread executes
Mutable global variables are the source of much non thread-safe code
You can give each thread a copy of a variable by using thread_local keyword to the static or extern
This will make a copy of the variable for each thread so that they cannot be writing to it at the same time

### Dynamic Storage Duration
You create dynamic objects with the new expression (i.e. new int{42})
You need to deallocate them with the delete expression
The object still exists for a bit after it has been deleted causing the program to appear to function correctly (pyside), but then when it’s cleaned up crash randomly. 

### Dynamic Arrays
The size can’t be changed but they have a dynamic storage duration
New MyType[n_elements] {init-list}
Returns a pointer to the first element of the array

### Exceptions
Types that communicate an error condition
Use throw keyword followed by throwable object like std::runtime_error

### Using try-catch Blocks
These are exception handlers for a block of code
Catch block specifies a handler for each exception type you can handle
It’s a way of anticipaoing that something might be thrown and logging it











