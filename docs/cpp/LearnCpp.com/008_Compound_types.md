# Compound Types

## Strings 

A **string** is a series of numbers. You can store it as a value using the std::string object from the string header. Strings will not automaticalyl convert to integer or floating point. 

One thing to note is that std::cin with strings does not work as expected because it breaks on whitespace. For that it's better to use std::getline() to input text.
```cpp
std:string name{};
std::getline(std::cin, age); //read a full line of text into age
```
This will read the entire line of the buffer after the std::cin prompt. Note that compining this with std::cin might have unexpected results, because the previous buffer might still be in the line. Because there is already something in the buffer std:cin resolves prematurely. This happens even if the user correctly only entered a single number, because of the newline character that is left in the buffer. A good rule of thumb is that after reading a vlue with std::cin, remove the newline from the stream. 
```cpp
std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n'); 
```

You can append string with the + operator or += to append a string to the end of an existing string. 

## Enumerated types

One of the simplest user-defined data types is the **enumeration type** aka **enum**. 
```cpp
enum Color{
    color_red,
    color_green
}
```

This enum would guarantee that your program can only run in christmas colors. 

You can't have the same value in multiple enums of the same namespace. 
```cpp
enum Color
{
  red,
  blue, // blue is put into the global namespace
  green
};

enum Feeling
{
  happy,
  tired,
  blue // error, blue was already used in enum Color in the global namespace
};
```
Each enumerator is automatically assigned an integer value based on it's index in the enumeration list. You can override this by assigning specific values to your enumerators, but you shouldn't because it can lead to confusion. 


You cannot input directly to an enum or cast an enum directly unless you use a static cast. (ugly). It's safe to use an unsigned int for enums. Enums are great for returning descriptive result of functions:

```cpp
enum ParseResult
{
    // We don't need specific values for our enumerators.
    success,
    error_opening_file,
    error_reading_file,
    error_parsing_file
};

ParseResult readFileContents()
{
    if (!openFile())
        return error_opening_file;
    if (!readFile())
        return error_reading_file;
    if (!parsefile())
        return error_parsing_file;

    return success;
}```

One problem with default enumerators is that they are not **type safe**. Entry 2 of enum color will resolve as true when compared to entry 2 of enum fruit. For this, c++ 11 defined a new concept, the **enum class**, which makes enumerations both strongly typed and strongly scoped. 

```cpp
enum class Color // "enum class" defines this as a scoped enumeration instead of a standard enumeration
{
    red, // red is inside the scope of Color
    blue
};

enum class Fruit
{
    banana, // banana is inside the scope of Fruit
    apple
};

Color color{ Color::red }; // note: red is not directly accessible any more, we have to use Color::red
Fruit fruit{ Fruit::banana }; // note: banana is not directly accessible any more, we have to use Fruit::banana

if (color == fruit) // compile error here, as the compiler doesn't know how to compare different types Color and Fruit
    std::cout << "color and fruit are equal\n";
else
    std::cout << "color and fruit are not equal\n";

return 0;
```

With enum classes you have to type out the scop of the enum. Generally it's better to use enum classes. 

## Structs

C++ allows us to creat our own user-defined **aggregate data types** which are types taht group multiple indifvidual variables together. The **struct** is one of the simples aggregate data types. 
```cpp
struct Employee{
    short id;
    int age;
    double wage;
};```

Each variable in the struct is called a **member**. Generally aggregate data types start with capital letter to distinguish them from variable names. You can give each instance of a struct an identifier and set the values

```cpp
Employee joe;
joe.id = 14;
joe.age = 32;
joe.wage = 24.15;
```

You can also initialize struct using an **initializer list**, which lets you set some or all members of the struct at declaration time.

```
Employee joe{ 1, 32, 60000.0 };
joe = {2, 32, 50000.0 }; //paycuts
```

Starting with C++11 you can give non-static (normal) struct members a default value

```
struct Rectangle{
    double length{ 1.0 };
    double width{ 1.0 };
}
```

Note that this will be incompatible with the initilization list. Because of that it's reccomended against using non-static member initilization. The useful thing about structs is that you can use them as a single argument in a function and pass a lot of date through with them. They can also contain other structs. 

To access structs in many files, it's common to put them into header files since they don't take up any memeory. These basic structs are what classes (future lesson) are built on. 

## Random number generation

a **pseudo-random number generator (PRNG)** is a program that takes a starting number **seed** and performs mathematical operations on it to transform it into some other number that appears unrealated to the seed. You should only seed your PRNG once, multiple times will cause the results to be less random or not random at all. 

std random number generator has std::srand() and std::rand(). The first is run at the top of your program, and std::rand() then keeps on generating new random numbers from that. Each time you give the same seed you get the same result back. The commonly accepted method for creating purely random seeds is the system clock. 
```
std::srand(static_cast<unsigned int>(std::time(nullptr)));
std::cout << std::rand() << '\t';
```

The number generated by std::rand() is alwasy between 0 and RAND_MAX, a constant in cstdlib that is usually 32767. To fit between two arbitrary values you ca ndo something like this:

```
int getRandomNumber(int min, int max){
    static constexpr double fraction { 1.0 / (RAND_MAX + 1.0) }; // static is more efficient, since we can cacluate this at compile time
    return min + static_cast<int>((max - min + 1) * (std::rand() * fraction));
}
```

The length of the sequence before a PRNG begins to repeat itself is known as the **preiod** of the PRNG. std::rand() is a mediocre PRNG - the results differ across systems. The main drawback is that RAND_MAX is usually set to 32767 which means it cannot generate numbers larger than this. A good prng is [Mersenne Twister](http://www.math.sci.hiroshima-u.ac.jp/m-mat/MT/emt.html). 

```cpp
#include <iostream>
#include <random> // for std::mt19937
#include <ctime> // for std::time

int main()
{
	// Initialize our mersenne twister with a random seed based on the clock
	std::mt19937 mersenne{ static_cast<std::mt19937::result_type>(std::time(nullptr)) };

	// Create a reusable random number generator that generates uniform numbers between 1 and 6
	std::uniform_int_distribution die{ 1, 6 };
	// If your compiler doesn't support C++17, use this instead
	// std::uniform_int_distribution<> die{ 1, 6 };

	// Print a bunch of random numbers
	for (int count{ 1 }; count <= 48; ++count)
	{
		std::cout << die(mersenne) << '\t'; // generate a roll of the die here

		// If we've printed 6 numbers, start a new row
		if (count % 6 == 0)
			std::cout << '\n';
	}

	return 0;
}
```

If you want to use random number generators across multiple functions, it's reccomended to create a global random number generator (in it's own namespace!). Or you could use a third party library like [Effolkronium's random library](https://github.com/effolkronium/random)







