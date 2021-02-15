# Introduction to inheritance

Inheritance is when one object is derived from another and changed some things yea. The class being inherited from is called the **parent/base/superclass** and the class doing the inheriting is called the **child/derived/subclass**. Inheritance represents an "is" relationship as opposed to "has-a" relationship. To inherit a baseball player from a person you do it like this

```cpp
class BaseballPlayer : public Person{
public:
    doubld m_battingAverage{};
    int m_homeRuns{};

    BaseballPlayer(double battingAverage = 0.0, int homeRuns =0)
        : m_battingAverage{battingAverage}, m_homeRuns{homeRuns}{}
}
```

You can chain inheritance as deeply as you like. New derived classes can be considered two part: One part base, and one part derived. Behind the scenes the most-base class at the top is constructed first. Then each child class is constructed in order, until the bottom. 

With non-derived classes, constructors only have to worry about their own members.

1. Memory for base is set aside
2. The appropriate Base constructor is called
3. The initialization list initializes variables
4. The body of the constructor executes
5. Control is returned to the caller. 

For a derived class there's one extra step

1. Memory for base is set aside
2. The appropriate Base constructor is called
3. **The base object is constructed first using the appropriate base constructor, if none is specified the default constructor will be used**. 
3. The initialization list initializes variables
4. The body of the constructor executes
5. Control is returned to the caller. 

C++ prevents classes from initializing inherited member variables. The values can only be set by the initializer of each respective class. This ensures that all variables are initialized only once. To initialize the Base class you do it like a member variable:

```cpp
class Derived: public Base{
public:
    double m_cost;

    Derived(double cost =0.0, int id=0){ // base requires id
        : Base {id}, m_cost{cost} {}
    }
}
```

It treats Base like a member variable of Derived. 


1.  Memory for derived is allocated.
2.  The Derived(double, int) constructor is called, where cost = 1.3, and id = 5
3.  The compiler looks to see if we’ve asked for a particular Base class constructor. We have! So it calls Base(int) with id = 5.
4.  The base class constructor initialization list sets m_id to 5
5.  The base class constructor body executes, which does nothing
6.  The base class constructor returns
7.  The derived class constructor initialization list sets m_cost to 1.3
8.  The derived class constructor body executes, which does nothing
9.  The derived class constructor returns

Constructors can only call constructors from their immediate parent/base class. Destructors are called in the reverse order like one would expect. 

## Inheritance and access specifiers

When you create a subclass, this class will have no access to private member variables. This is where the **protected** access specifiers comes in: it allows the subclass to access the members, but nothing outside it. Because refactoring would be a huge issue without getters and setters, this is best suited for small teams with a limited scope of derived classes. It saves some effort on interface design. 

Just like member variables, there are three different access specifiers for inheriting from classes. 

```cpp
class Pub: public Base {};
class Pro: protected Base {};
class Pri: private Base {};
class Def: Base {}; // defaults to private inheritance
```

Public inheritance is the most commonly used. It keeps all the variables from the parent class in the same state as in the parent class. Protected makes them all protected, and private makes them all private. You should use public inheritance unless you have a specific reason to do otherwise.

You can redefine behaviour in subclasses. Note that the derived functions still have the same access as the subclass. You can extend the behaviour of a function by calling the base class function inside the derived function.

```cpp
class Derived: public Base {
public:
    Derived(int vluae)
        :Base(value){}

    int getValue() { return m_value } // m_value is protected
    void identify(){
        Base::identify();
        std::cout << "I am a Derived\n";
    }
}
```

You can always static_cast your subclasses to their parent classes. This is especially useful for friend functions where you want to call the baseclass. 

You can hide members of the base class by changing their access specifier. 

```cpp
class Base
{
private:
    int m_value;

public:
    Base(int value)
        : m_value(value) {}

protected:
    void printValue() { std::cout << m_value; }
};

class Derived: public Base{
public:
    Derived(int value)
        : Base(value) {}

    using Base::printValue // printValue is now public in the subclass
}
```

Of course you cannot change private members to be public as well. 

## Multiple inheritance

What if you want to have **many** parents?? **Multiple inheritance** enables a derived class to inherit members from multiple parent classes. Multiple inheritance is maintenance nightmare. There is ambiguity for when both parent classes have a similarly named member. Because of this you need to specify which parent class's member to call *during the function call*. 

There is also the **diamond of doom** problem: what if a subclass inherits from two parent classes that both inherit from the same parent. Most of the issues can be addressed through scoping, but the overall complexity still skyrockets. 

!!! note 
    Avoid multiple inheritance unless alternative lead to more complexity











