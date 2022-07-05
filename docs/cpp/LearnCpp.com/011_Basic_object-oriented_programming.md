# Basic object-oriented programming

Traditional programming, programs are basically a list of instructions with the semantics defined by the user:

```cpp
driveTo(you, work);
```

Obects are the conceptual definitions we use to sort reality. Object have major components which defined them as different or similar to other objects. Objects also have behaviours that they can exhibit (being opened, making something hot, etc)

```cpp
you.driveTo(work)
```

This reads more clearly and makes it clear what the subject of the instructions is (you). 

## Classes 

**Classes** are similar to structs in that they hold data, but they can also hold a lot more, like functions. The class keyword defines a new user-defined class. 

```cpp
struct DateStruct{
    int year{};
    int month{};
    int day{}
};

class DateClass{
public:
    int m_year{};
    int m_month{};
    int m_day{};
};
```

The only significan difference is the public keyword in the class. Just like struct declarations, class delcarations do not allocate any memory. Class and struct definitions are like blueprints, they describe what the resulting object will look like, but do not actually create the object. 

The big benefit of classes are that they can hold functions, called **member functions** or **methods**. 

```cpp
class DateClass{
public:
    int m_year{};
    int m_month{};
    int m_day{};

    void print(){
        std::cout << m_year << '/' << m_month << '/' << m_day;
    }
};

int main(){
    DateClass today {2020, 10, 14};
    today.m_day = 16;
    today.print();
}
```

Note that within the function you do not need to use it's namespace to access the member variables. These are called **implicit objects**. 

!!! note
    Name your classes starting with a capital letter.

You can call your functions out of the order they are defined within the function. The following works

```cpp
class foo{
public:
    void x() { y(); }
    void y() {};
};
```

Classes can have **member types** (including type aliases). You can also nest classes within other classes, though the cases for using this are rare. Structs can also have many of these associated member function in C++, but they do not clean up after themselves. 

!!! note
    Use the struct keyword for data-only structures. Use the class keyword for objects that have both data and functions. 

all of the elements of the standard library are also classes!

#### Public vs. Private

**Public members** are members of a struct or class that can e accessed from outisde of the struct or class. If they are not explicitely declared as public and you try to access the variables otuside of the class, the program will not compile. The variables not after the public: keyword are considered **Private members**. The public keyword with the : is called an **access specifier** - these dtermine who has access to the members that follow the specifier. There are three access specifier keywords:

* public
* private
* protected

You can (and will) use multiple access specifiers to set the access levels of each member. The default member variable is private. 

!!! note
    Make member variables private, and member functions public, unless you have a good reason not to. 

The group of public member variables are commonly referred to as the **public inteface**. This interface determines how we interact with the class. One interesting nuance of C++ is that every object instance has the same rights to every instance of that project. This means that if a function inside a class of object A can access variable foo, it can also access variable foo of object B. 

The big difference between structs vs classes here it is:

!!! note
    A struct default is members to public but a class to private

## Encapsulation

Why have private member variables at all? This can seem strange at first, but having less ways to interact with an object allows it to be simplified in the context of large code. It's abstraction of a concept, so you don't need to remember or know the entire inner workings of objects. This concept is referred to as **encapsulation** in object oriented programming, or information hiding. This way users can use the object without understanding how it is working. 

#### Benefits of encapsulation

* Easier to use and reduction of program complexity. You can safley forget how a class is working internally, and just use the public interface. This dramatically reduces teh complexity of programs, and reduces mistakes. 

* Protect your data and pervent misuse. This allows you a guarantee over your utility variables, and extends the assumptions that you can make about them. For example a variable m_length should maybe always be the length of a string. If a user sets this variable incorrectly, other parts of the class might not work. This can force users to use the public functions instead of the varialbes themselves, and allows implementation of a validation layer. This is very similar to the at() functions of std::array and std::vector!

* Encapsulated classes are easier to change. You can keep legacy functions by simply re-directing them to refactored and updated versions. 

* Encapsulated classes are easier to debug. If there are less ways to use a class, it's much easier to find errors in it. 

Depending on the class, it may or may not be appropriate to set variables directly. Note that if you are setting them directly, it's a commitment to never change that variable name. Because of this, it is common to wrap your variables in **access functions**, which get/set the functionality of that variable instead. There are two types of access functions: **getters** and **setters**. Getters should always return by value or const reference as "read-only" access to data. Otherwise the getter would allow the user to change the variable effectively circumventing the setter function. 

Some general rulse which can help decide wether to implement getter/setter functions:

* If nobody outside the class needs access to the member, don't provide access functions
* If access is required for the memeber, think about whether you can expose a behaviour or action instead. 
* Consider providing only a getter. 

## Constructors

If all members of a class are public, we can use **aggregate initialization** to initialize the class with a list or uniform initialization. This stops working as soon as some variables become private. **Constructors** are a special kind of class member function that are called when an object is instantiated. Typically they are used to initialize class variables. The default costructor is what is called when no arguments are used when creating the class. It is called the same as the class. 

```cpp
class Fraction{
private:
    int m_numerator;
    int m_denominator;
public:
    Fraction(){
        m_numerator = 1;
        m_denominator = 1;
    }
};
```

Of course constructurs can also take inputs. These now allow the classes to be initialized with direct or list initialization:

```cpp
fraction fiveThirds{5,3};
Fraction threeQuaters(3,4);
```

Consider how you might keep the number of constructors down through smart defaulting of variables when you implement your constructor. If there is no constructor provided, C++ will generate a default one. You can initialize member variables as you define them when you don't have a constructor or there is no special logic in setting them. If you don't need a construct use = default;

```
class Date{
private:
    int m_year{ 1900 };
    int m_month{ 1 };
    int m_day{ 1 };
public:
    Date() = default;

    Date(int year, int month, int day){
        m_year = year;
        m_month = month;
        m_day = day;
    }
}
```

This is better because it's more explicit, and it guarantees that there will be an empty constructor wether there are other constructors or not. 

If classes are nested, the constructors of the internal classes will be called first. 

!!! note 
    Always initialize all member variables in your objects

One problem with assigning all member variables in your constructor, is that it won't work with const vars. Because of this and style in general, it's reccomended to use a **member initializer list** - which is different than a regular initializer list. A member initializer list is almost identical to doing direct initialization or unitform initialization. 

```cpp
class Something {
private:
    int m_value1;
    double m_value2;
    char m_value3;
public:
    Something(int value1, double value2, char value3='c') : 
        m_value{ value1 }, m_value2{ value2 }, m_value3{ value3 }{
        // no need for assignment here
    } \\Initialize our member variables
}
```

!!! note
    Use member initializer lists to initialize your class member values instead of assignment. 


Depending on how many arguments your member initializer list has you can put the whole thing on one line, two lines () and :{}, or one line for each value with commas at the end of each. 

* Don't initialize member variables in such a way that they are dependent upon other member variables being initialized first. 
* Initialize variables in the member initialization list in the same order that they are declared in your class. This isn't required but makes sense.

In general for non-static member varialbes you should intialize them when they are defined, and possibly overwrite them with the constructor. Static members are pretty rare anyway, and if you run into them you're probably going to have some extra logic around that anyway. 

!!! note
    Favor use of non-static member initialization to give default values for your member variables. 

To avoid duplicate code we might try the following:

```cpp
class Foo{
public:
    Foo(){
        // code  to do A
    }

    Foo(int value){
        Foo();
        // code to do B
    }
}
```

Surprisingly this will not work as you expect. What's happening is that the second constructor is creating a new Foo() object, which is immediately discareded. To combat this problem we use **constructor chaining**, the process of which is called **delegating constructors**. The above problem would be fixed like this:

```cpp
class Foo{
public:
    Foo(){
        // code to do A
    }

    Foo(int value): Foo{}{
        // code to do B
    }
}
```

!!! note
    If you have multiple constructors that have the same functionality, use delegating constructors to avoid duplicate code. 

This is how it's generally done:

```cpp
class Employee{
private:
    int m_id{};
    std::string m_name{};

public:
    Employee(int id=0, const std::string &name=""):
        m_id{id}, m_name{name}{
            init(name);
        }
    
    Employee(const std::string &name) : Employee{0, name} { }

    init(const std::string &name){
        std::cout << "Employee " << m_name << " created. \n";
    }
};
```

The only caveaut with this is that anybody can re-init your object anytime. So you might consider makint init a private member function. 

## Destructors

Similar to how a constructor is called when creating objects, a **destructor** is called when the object is destroyed. This is espcially usefull when cleaning up any arrays created by the object. 

* The destructor must have the same name as the class preceded by a ~
* The destructor can not take arguments
* The destructor has no return type

You can safely call member functions with the destructor, as it runs just before the object is removed. Interior classes are deconstructed first in the opposite order to how the constructors create them. 

RAII (Resource Acquisition Is Initialization) is a programming technique that has the constructor allocating "new" and then the destructor clearning it up. 

How do classes know to call their own member varialbes?

```cpp
simple.setId(2)
```

Is converted by the compiler to

```cpp
setId(&simple, 2);
```

and setId likewise

```cpp
void setID(intid) { m_id = id; }
// converted to
void setID(Simple* const this, int id) { this->m_id =id; }
```

The **this pointer** is a hidden const pointer that holds the address of the object the member function was called on. The compiler add this-> to all class members inside of the classes. "this" always points to the object being operated on. Often people will explicitly reference "this", but I prefer the m_ prefix to member variables for better readability. 

If your member functions return a *this, you can chain them together similar to std::cout, which does that as well by returning the *this pointer. That way if you chain them together they will always resolve to themselves:

```cpp
class Calc{
private:
    int m_value{};
public:
    Calc& add(int value) { m_value += value; return *this; }
    Calc& sub(int value) { m_value -= value; return *this; }
    Calc& mult(int value) { m_value *= value; return *this; }

    int getValue() { return m_value; }
}

Calc calc{};
calc.add(5).sub(3).mult(3);

std::cout << calc.getvalue() << '\n';
```

This allows very readable compression into a single expression. 

## Class code and header files

As classes get longer and more complicated, it becomes distracting having the implementation code next to the defenitions. Because of this it's common to defined member functions outside of the class itself.

```cpp
class Date
{
private:
    int m_year;
    int m_month;
    int m_day;
 
public:
    Date(int year, int month, int day);
 
    void SetDate(int year, int month, int day);
 
    int getYear() { return m_year; }
    int getMonth() { return m_month; }
    int getDay()  { return m_day; }
};
 
// Date constructor
Date::Date(int year, int month, int day)
{
    SetDate(year, month, day);
}
 
// Date member function
void Date::SetDate(int year, int month, int day)
{
    m_month = month;
    m_day = day;
}
```

This way of seperating the definitions of the member functions from the class makes it much easier to put the class for forward-declaration into a header file. This doesn't violoate the one-definition rule because if the header file has proper header guards, it shoulnd't be possible to include the class definition more than once in the same file. 

Defining member functions in the head does not violate the one-definition rule, because they are considered implicitly inline. This means it's no problem defining trivial member functions (such as getters/setters) inside the class definition itself. 

* For classes used in only one file that aren't generally reusable, defien them directly in the single .cpp file they're used int. 
* For classes used in multiple files, or intended for general reuse, define them in a .h file that has the same name as the class
* Trivial member functions (trivial cnostructors or destructors, acces functions, etc...) can be defined inside the class. 
* Non-trivial member functions should be defined in a .cpp file that has the same name as the class. 
* Default parameters for member function should be declared int he class definitions (.h) where they can be seen by whomever #includes the header.


The examples in this course usually have the classes defined in the main .cpp file for ease of illustration, but larger code project should have everything structured into headers etc. 

A "header only" library is a library of .h files for your syntax comletion and illustration of the objects, and a pre-compiled binary. this makes it faster to link and recomiple, and it can be shared with many applications. It also makes it less likely that people will steal the code. 

## Const class objects and member functions

The guy who wrote doom says we should basically const all our functions and classes. What does this mean exactly?

If you instantiate the class using the const keyword, any modification of the member variables of the object is disallowed. We're familiar with this behaviour and have already been using it. 

This isn't what mr. doom meant tho, he meant consting member functions. This is because consting a class makes so you can't call any of the class' non const member functions! A **const member functions** is a member function that guarantees it will not modify the object or call any non-const member functions. To make a function a const member function, just append the const keyword to the function prototype after the parameter list, but before the function body:

```cpp
class Something{
public:
    int m_value;

    Something(): m_value{0} {}

    void resetValue() { m_value = 0; } 
    void setValue(int value) { m_value = value; }

    int getValue() const {return m_value;}
};
```

!!! note
    Make any member function that does not modify the state of the class object const, so that it can be called by const objects. 

The const keyword must be used in both the function prototype in the class defintion and on the function definition. You can overload functions for different behaviour for const and non-const versions of the class. 

## Static member variables

When you instnatiate a class, each instance gets its own copy of all normal member variables. When you make a member variables static, it is the same reference across all instances of the object. Static members are not associated with class objects, they are create when the program starts and destroyed when the program ends. They can also be accessed directly without creating an instance of the class

```cpp
class something{
public:
    static int s_value;
};

int Something::s_value {1};

int main (){
    Something::s_value = 2;
    std::cout << Something::s_value << '\n';
    return 0;
};
```

This can be useful for something like a unique id counter for the duration of the program...

When you declare the static member variable in the function, you are informing the complier of its existence, but you still need to define it outside of the class. If the static member is a const integral type, or const enum, you can initialize it inside the class definitions. constexpr can also be initialized directly inside the class def. 

```
class Whatever{
public:
    static const int s_value{ 4 };
    static constexpr std::array<int, 3> s_array{ 1, 2, 3 };
};
```

Static member variables can also be useful when using an internal lookup tables (a list of pre-calculated values) - this will keep that value in memory only once no matter how many isntances of the class. 

## Static member functions

In order to access a private static member variable, you need a static member function. Just like the variables, they are not attached to any particular object, but to the class definition itself. You could also instantiate a member of the class and then call a function to access the variable, but that's not very clean. 

```cpp
class Something{
private:
    static int s_value;
public:
    static int getValue() {return s_value;}
};

int Something::s_value { 1 }; // init the variable

int main(){
    std::cout << Something::getValue() << '\n'; // no Something object instance required
}
```

Because they are global in scope, static member functions have no *this pointer. This makes sense because static member function do not work on an object. 

Static member functions can directly access other static member functions or variables, but not non-static members. This is because the non-static members must belong to a class object, and not class object has been instantiated yet. 

C++ does not support static constructors. If you can, you should directly initialize your static variables. If you need to initialize your static variables with a function (which would run when the program is initialized) you can do it with a lambda function after the class declaration and call it immediately. 

## Friend functions and classes

Even though you might seperate code into each of your functions, there are some cases where you might want other closely-knit functions or classes have access to your class' private members. In these situations, there are two options:

* Have the classes only use the publicly exposed functions. The downside is that this could clutter up the ui of the classes. You should never expose members that you don't think any user should be allowed to use. 

* Use friend classes and friend functions. This lets other classes and functions access a class' private members.

```cpp
class Accumulator{
private: 
    int m_value;
public:
    Accumulator() { m_value = 0; }
    void add(int value) { m_value += value; }
    // Make the reset() function a friend of this class
    friend void reset(Accumulator & accumulator);
};

// now reset can access the private member variable
void reset(Accumulator & accumulatro){
    accumulator.m_value = 0;
}
```

Of course if the friend function is not part of the class, you won't have a *this pointer and will need to pass a reference to the object. You can also make entire classes friends of each other. If you want to make certain member functions friends of each other, you will have to have them be defined first with forward declaration. This hassle only happens if you are trying to define more than one class in a file, which wouldn't happen that often anyway. 


## Anonymous objects

Anonymous objects are objects that are never stored in a variable. Example:

```cpp
int add(int x, int y){
    int sum{ x + y };
    return sum; // sum is a placeholder
}

int add(int x, int y){
    return x + x; // anonymous object is created
}
```

All anonymous objects have "expression scope" - they are created, evaluated, and destroyed in a single expression. 

You can manually create anonymous objects by instancing objects and just ommitting the variable name

```cpp
Cents cents{ 5 };
Cents{ 7 }; //anonymous
```

This is just really useful for ommiting temp variables and making the code more concice. 

## Timing your code

C++ comes with chrono, which is a bit of an archic timing functionality. We can wrap it up with a class like this:

```cpp
#include <chrono>

class Timer
{
private:
	// Type aliases to make accessing nested type easier
	using clock_t = std::chrono::high_resolution_clock;
	using second_t = std::chrono::duration<double, std::ratio<1> >;

	std::chrono::time_point<clock_t> m_beg;

public:
	Timer() : m_beg(clock_t::now())
	{
	}

	void reset()
	{
		m_beg = clock_t::now();
	}

	double elapsed() const
	{
		return std::chrono::duration_cast<second_t>(clock_t::now() - m_beg).count();
	}
};

int main()
{
    Timer t;

    // Code to time goes here

    std::cout << "Time elapsed: " << t.elapsed() << " seconds\n";

    return 0;
}
```

Note that when benchmarking your code you should be using release builds only. Debug builds can be less performant in different ways than release builds. Always measure things at least 3 times to account for background processes and other things that might be effecting performance. Also note that these results will only be valid for your machines specific architecture. 






























