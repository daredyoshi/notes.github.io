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

