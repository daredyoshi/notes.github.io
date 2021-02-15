# Virtual Functions

We can creat pointers to child classes, and they produce the expected result

```cpp
#include <iostream>

int main()
{
    Derived derived{ 5 };
    std::cout << "derived is a " << derived.getName() << " and has value " << derived.getValue() << '\n';

    Derived &rDerived{ derived };
    std::cout << "rDerived is a " << rDerived.getName() << " and has value " << rDerived.getValue() << '\n';

    Derived *pDerived{ &derived };
    std::cout << "pDerived is a " << pDerived->getName() << " and has value " << pDerived->getValue() << '\n';

    return 0;
}
```

Derived is a class inheriting from base. What's interesting, is that we can also create pointers to the base class

```cpp
Derived derived{ 5 };
 
// These are both legal!
Base &rBase{ derived };
Base *pBase{ &derived };

std::cout << "derived is a " << derived.getName() << " and has value " << derived.getValue() << '\n';
std::cout << "rBase is a " << rBase.getName() << " and has value " << rBase.getValue() << '\n';
std::cout << "pBase is a " << pBase->getName() << " and has value " << pBase->getValue() << '\n';
```

These base pointers cannot see any of the functions or members of the Derived class. This is extremely useful for classes where a lot of subclasses can be input to the same functions. If you have a base class Animal with Cat and Dog inheriting from it, you would have to write an overloaded print function that takes each of the animal classes. 

```cpp
void print(const Cat &cat){
    std::cout << cat.getName() << " says " << cat.speak() << '\n';
}

voic print(const Dog &dog) etc..
```

This would get really annoying with multiple animal types. Instead, we can do something like this:

```cpp
void print(const Animal &animal){}
```

The problem now, of course, is that we don't know what the specific animals' sound is. Virtual functions can solve this. A **virtual function** is a special type of function that, when called resolves to the most-derived version of the function that exists between the base and derived class. This is called **polymorphism** because it's an adaptive function to the current definition of the class. It respects the **override** functions of the child classes, and calls those instead if they exist. You create them with the virtual keyword.

```cpp
class Base{
public:
    virutal std::string_view getName() const { return "Base"; } // the virtual keyword tells the class to always use the most override
};

class Derived: public Base{
public:
    virutal std::string_view getName() const { return "Derived"; }
}

int main(){
    Derived derived;
    Base &rBase{ derived };
    std::cout << "rBase is a " << rBase.getName() << '\n';
    return 0;
}
```

This prints ```rBase is a Derived``` - even though we cast derived to a reference of it's base class. This is based on the object that is referencing. If you have further derived classes, it won't call them just because they exist. It calls the most overridden one of this particular instance of the base class. 

!!! note
    While the virtual keyword on the derived class is not required for it to work, it's good practice to keep it there to make it more obvious what's going on.

!!! note
    Do not call virutal functions from constructors or destructors. Because the classes have not all bee initialized yet, they won't be able to call each other as expected. Same goes for destructors. 

!!! note
    Only make the functions virtual that require it. It's more overhead to resolve virtual functions.

Because you sometimes want an override function to be slightly different than the function it's overriding (say it's const or a minor difference) there is the **override specifier** to force it to override. This goes right after where the const specifier would go. 

```cpp
class A{
public:
    virtual const char* getName1(int x) { return "A"; }
    virtual const char* getName2(int x) { return "A"; }
    virtual const char* getName3(int x) { return "A"; }
}

class B : public A{
public:
    virutal const char* getName1(short int x) override { return "B"; } // this will now produce a compile error
}

```

!!! note
    Use the override specifier for every inteded override function you write. This makes it easier to read and catches more errors. 

If you do not want someone to be able to override a function any further, you can use the **final specifier** in the same place as the override specifier. 

```cpp
class A
{
public:
	virtual const char* getName() { return "A"; }
};

class B : public A
{
public:
	// note use of final specifier on following line -- that makes this function no longer overridable
	virtual const char* getName() override final { return "B"; } // okay, overrides A::getName()
};

class C : public B
{
public:
	virtual const char* getName() override { return "C"; } // compile error: overrides B::getName(), which is final
};
```

Here you can see there is a compile error coming up when attempting to override a function that was set to final. Note that without the override keyword this would not produce the error, but getting a ref to A would call B.getName().

There is a way to allow for different return types. If the return type of a virtual function is a pointer or a reference to a class, override functions can return a pointer or a reference to a derived class. These are called **covariant return types**. In the following because Derived "is-a" base it's allowed:

```cpp
class Base
{
public:
	// This version of getThis() returns a pointer to a Base class
	virtual Base* getThis() { std::cout << "called Base::getThis()\n"; return this; }
	void printType() { std::cout << "returned a Base\n"; }
};
 
class Derived : public Base
{
public:
	// Normally override functions have to return objects of the same type as the base function
	// However, because Derived is derived from Base, it's okay to return Derived* instead of Base*
	Derived* getThis() override { std::cout << "called Derived::getThis()\n";  return this; }
	void printType() { std::cout << "returned a Derived\n"; }
};
```

In the above example, you only get a Derived* if you call getThis() with an object that is typed as a Derived object. 

```cpp
Derived d{};
Base* b{ &d };
d.getThis()->printType(); // calls Derived::getThis(), returns a Derived*, calls Derived::printType
b->getThis()->printType(); // calls Derived::getThis(), returns a Base*, calls Base::printType
```

## Virtual destructors, virtual assignment, and overriding virtualisation

When creating your own destructors, you should **always** make them virtual when dealing with inheritance. 

```cpp
class Base
{
public:
    virtual ~Base() // note: virtual
    {
        std::cout << "Calling ~Base()\n";
    }
};

class Derived: public Base
{
private:
    int* m_array;

public:
    Derived(int length)
      : m_array{ new int[length] }
    {
    }

    virtual ~Derived() // note: virtual
    {
        std::cout << "Calling ~Derived()\n";
        delete[] m_array;
    }
};
```

Without the virtual keyword, that array would not be cleaned up. 
* If you intend your class to be inherited from, make sure your destructor is virtual
* If you do not intend your class to be inherited from, mark your class as final.

What about when you want to ignore virtualisation? 

```cpp
Derived derived;
const Base &base { derived };
// Calls Base::GetName() instead of the virtualized Derived::GetName()
std::cout << base.Base::getName() << '\n';
```

## Early binding, late binding (optional)

**Binding** is when variables and function names are bound to addresses in memory. Direct function calls are bound early, because the compiler knows example how much memory they will take. This is the same as static binding. In some programs, you don't know which function will be called until runtime. This is called **late binding**. The following code is an example:

```cpp
#include <iostream>

int add(int x, int y)
{
    return x + y;
}

int main()
{
    // Create a function pointer and make it point to the add function
    int (*pFcn)(int, int) = add;
    std::cout << pFcn(5, 3) << std::endl; // add 5 + 3

    return 0;
}
```

The compiler doesn't know which function will be called until the execution of the program. This is slightly less efficient, because it involves an extra level of indirection. 

Virtual functions use a special form of late binding known as the **virtual table**: a lookup table of functions used to resolve function calls in a dynamic/late binding manner. Each class has a static array of function pointers defined at compile time, with one entry for each virtual function that can be called by objects of the class. There is also a special hidden pointer to the base class ```*__vptr```. This is setup automatically and is inherited by derived classes. 

Consider the following example

```cpp
class Base
{
public:
    FunctionPointer *__vptr; // this is created automatically we are just adding it to be explicit
    virtual void function1() {};
    virtual void function2() {};
};

class D1: public Base
{
public:
    virtual void function1() {};
};

class D2: public Base
{
public:
    virtual void function2() {};
};
```

In this example each class has a virtual table of 2 function points. ```*__vptr``` is set to point to the virtual table for that class. By default each entry to that table is filled with the most derived version for that class. 

![virtual_table](./VTable.gif)

## Pure virtual functions, abstract base classes, and interface classes

You can also create pure virtual functions to enable expansion of your classes. For example adding hooks inside your functions that you can potentially extend later. You create them by simply assigning them 0.

```cpp
class Base{
public:
    const char* sayHi() const { return "Hi";}
    virtual int getValue() const = 0; // a pure virutual function
}
```

We are basically saying "it is up to the derived classes to implement this function"  - any class with one or more pure virtual functions becomes an **abstract base class*. This means it can not be instantiated! There would be no way to resolve this classes function. Because of this only child subclasses of the class can be instantiated. You can give virtual functions a body by defining it after the class 

```cpp
class Animal // This Animal is an abstract base class
{
protected:
    std::string m_name;

public:
    Animal(const std::string& name)
        : m_name{ name }
    {
    }

    std::string getName() { return m_name; }
    virtual const char* speak() const = 0; // The = 0 means this function is pure virtual

    virtual ~Animal() = default;
};

const char* Animal::speak() const  // even though it has a body
{
    return "buzz";
}
```

This can be useful when we want there to be a defined default implmentation, but want to force an explicit calls to it. 

### Interface class

An **interface class** is a class that has no member variables, and where all of the functions are pure virtual. It's pure definition, with no implmentation. This is useful when you want to define the functionality that derived classes must implmenet, but leave the details to the derived class. They are typically named beginning with an I. 

```cpp
class IErrorLog{
public:
    virtual bool openLog(const char *filename) =0;
    virutal bool closeLog() = 0;
    
    virtual bool writeError(const char *errorMessage) = 0;

    virtual ~IErrorLog() {} // make a virtual destructor in case we delete  an IErrorLog pointer, so the proper derived desturctor is called.
}
```

This way you can easily refactor the error in case you want to use a different logger. Don't forget to include virtual destructors for your interface classes. 

If you want to share a base class between multiple child classes, you can create a virutal base class. This means there is only one base object if you choose to use multiple inheritance. 

```cpp
class PoweredDevice
{
public:
    PoweredDevice(int power)
    {
		std::cout << "PoweredDevice: " << power << '\n';
    }
};
 
class Scanner: virtual public PoweredDevice // note: PoweredDevice is now a virtual base class
{
public:
    Scanner(int scanner, int power)
        : PoweredDevice{ power } // this line is required to create Scanner objects, but ignored in this case
    {
		std::cout << "Scanner: " << scanner << '\n';
    }
};
 
class Printer: virtual public PoweredDevice // note: PoweredDevice is now a virtual base class
{
public:
    Printer(int printer, int power)
        : PoweredDevice{ power } // this line is required to create Printer objects, but ignored in this case
    {
		std::cout << "Printer: " << printer << '\n';
    }
};
 
class Copier: public Scanner, public Printer
{
public:
    Copier(int scanner, int printer, int power)
        : PoweredDevice{ power }, // PoweredDevice is constructed here
        Scanner{ scanner, power }, Printer{ printer, power }
    {
    }
};

PoweredDevice: 3
Scanner: 1
Printer: 2
```

Couple more things to note on this:

* Virtual Base classes are always created before non-virtuali base classes.  
* The virutal intermediary subclasses still have calls to the constructors of the superclasses. These are in case we create an instance of those classes.
* The *most* derived class is responsible for constucting the virtual base class. 
* All classes inheriting a virtual base class will have a virtual table and be larger by a pointer.


You can **slice objects** by initializing their parent class with them. This keeps all the varialbes of the parent class, but effectivly trims out all the elements of the subclass. It's easy to accidentally slice object when not passing them into functions by reference. It's also easy to slice objects when putting them into a vector. 

```cpp
std::vector<Base> v{};
v.push_back(Base{ 5 });
v.push_back(Derived{ 6 }); // Derived gets sliced to base only
```

You can't fix this by passing in a reference to the vector, but you could make it a vector of pointers, or use std::reference_wrapper

#### the Frankenobject

```cpp
Derived d1{ 5 };
Derived d2{ 6 };
Base &b{ d2 };

b = d1; // this line is problematic
```

This kind of assignment is not virtual. It will slice d1 to a Baseobject. Even more problematic, is if the inheritance chance was longer. The "top" of the slice would still exist in memory and couldn't be deleted. 

## Dynamic casting

What if you want to access some information in a derived class but you only have a pointer to a base class. The opposite of **upcasting**, **downcasting** is when you **downcast** a pointer to a child class. This is done with an operator called dynamic_case<Derived*>(base). 

!!! note
    Always ensure your dunamic casts actually succeeded by checking for a null pointer result. 

```cpp
Base *b{ getObject(true) };

    Derived *d{ dynamic_cast<Derived*>(b) }; // use dynamic cast to convert Base pointer into Derived pointer

    if (d) // make sure d is non-null
        std::cout << "The name of the Derived is: " << d->getName() << '\n';

delete b;
```

This will not work with:

* Protected or private inheritance
* For classes that do not declare or inherit any virtual functions (and thus don't have a virtual table)
* Certain classes involving virtual base classes. 

You can also downcast with static_cast, but it's not guaranteed to work. dynamic_cast can also work with references in the same way with references. 

!!! note
    Use static_cast unless you're downcasting, or avoid casting alltogether and use virtual functions

Times when downcasting is better than using a virtual function

* When you cannot modify the base class to add a virtual funcctions
* When you need access to something that is derived-class specific (like a getter that only this one class needs)
* When adding a virtual function to your base class doesn't make sense. 


## Can we make Operator "<<" virtual?

Nope. Only member functions can be virtualized, and those can't return a std::out out&
The fix is to delegate the printing to a virtualized function!

```cpp
#include <iostream>
class Base
{
public:
	// Here's our overloaded operator<<
	friend std::ostream& operator<<(std::ostream &out, const Base &b)
	{
		// Delegate printing responsibility for printing to member function print()
		return b.print(out);
	}
 
	// We'll rely on member function print() to do the actual printing
	// Because print is a normal member function, it can be virtualized
	virtual std::ostream& print(std::ostream& out) const
	{
		out << "Base";
		return out;
	}
};
 
class Derived : public Base
{
public:
	// Here's our override print function to handle the Derived case
	virtual std::ostream& print(std::ostream& out) const override
	{
		out << "Derived";
		return out;
	}
};
 
int main()
{
	Base b{};
	std::cout << b << '\n';
 
	Derived d{};
	std::cout << d << '\n'; // note that this works even with no operator<< that explicitly handles Derived objects
 
	Base &bref{ d };
	std::cout << bref << '\n';
 
	return 0;
}
```












