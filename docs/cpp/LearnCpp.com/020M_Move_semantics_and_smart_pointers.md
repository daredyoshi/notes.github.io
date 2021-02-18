# Move semantics and smart pointers

It's pretty easy to forget to deallocate pointers. Even if you don't forget, there are many ways that pointers aren't deleted when functions exit early. If we wrap pointers in a class, then the destructor can automatically delete the pointer when the class goes out of scope. This sort of class is called **smart pointer**: a composition class that is designed to manage dynamically allocated memory and ensure that this memory gets deleted when the smart pointer object goes out of scope. 

The problem is if the pointer gets copied, then it's destroyed twice and the program will crash. This is because the default copy constructor will do a shallow copy of the value of the pointer. Both pointers are then pointing to the same resource. It will also happen if you are passing in parameters by value. 

The core idea behind **move semantics** is having our copy constructor and assignment transver/move ownership of the pointer from the source to the destination object. It means the class will transfer ownership of the object rather than make a copy. 

```cpp
#include <iostream>

template<class T>
class Auto_ptr2
{
	T* m_ptr;
public:
	Auto_ptr2(T* ptr=nullptr)
		:m_ptr(ptr)
	{
	}

	~Auto_ptr2()
	{
		delete m_ptr;
	}

	// A copy constructor that implements move semantics
	Auto_ptr2(Auto_ptr2& a) // note: not const
	{
		m_ptr = a.m_ptr; // transfer our dumb pointer from the source to our local object
		a.m_ptr = nullptr; // make sure the source no longer owns the pointer
	}

	// An assignment operator that implements move semantics
	Auto_ptr2& operator=(Auto_ptr2& a) // note: not const
	{
		if (&a == this)
			return *this;

		delete m_ptr; // make sure we deallocate any pointer the destination is already holding first
		m_ptr = a.m_ptr; // then transfer our dumb pointer from the source to the local object
		a.m_ptr = nullptr; // make sure the source no longer owns the pointer
		return *this;
	}

	T& operator*() const { return *m_ptr; }
	T* operator->() const { return m_ptr; }
	bool isNull() const { return m_ptr == nullptr;  }
};

class Resource
{
public:
	Resource() { std::cout << "Resource acquired\n"; }
	~Resource() { std::cout << "Resource destroyed\n"; }
};

int main()
{
	Auto_ptr2<Resource> res1(new Resource());
	Auto_ptr2<Resource> res2; // Start as nullptr

	std::cout << "res1 is " << (res1.isNull() ? "null\n" : "not null\n");
	std::cout << "res2 is " << (res2.isNull() ? "null\n" : "not null\n");

	res2 = res1; // res2 assumes ownership, res1 is set to null

	std::cout << "Ownership transferred\n";

	std::cout << "res1 is " << (res1.isNull() ? "null\n" : "not null\n");
	std::cout << "res2 is " << (res2.isNull() ? "null\n" : "not null\n");

	return 0;
}
```

!!! note
    Avoid std::auto_ptr. This implmenents move semantics through the move and copy constructor. Passing it by value to a function will cause your resource to get moved to the function parameters (and destroyed when the function goes out of scope). It also deletes its contents using non-array delete, which means it won't work with dynamically allocated arays. It also doesn't play nice with a lot of the other classes in the std library. Consider it deprecated and don't use it.

Before C++11 there was no mechanism to differentiate "copy semantics" from "move semantics". Because of this the concept of "move" was formally defined and move semantics added to the language to properly differentiate copying from moving. The std::auto_ptr has now been replaces with std::unique_ptr, std::weak_ptr and std::shared_ptr.


## R-value references

To understand move semantics we need to revisit the topic of r-values and l-values. Every expression in C++ has two properties: a type, and a **value category**. The value category is used for certain kinds of syntax checking. 

An **l-value** can be thought of as a function or object (or expression that evaluates to this). All l-values have memory addresses. They were originally defined as "values that are suitable to be on the left-hand side of an assignment expression". After the const keyword was added to the language, l-values were split into two sub-categories: modifiable and non-modifiable. 

An **r-value** is pretty much anything that is not an l-value. This includes literals, temp values, and anonymous objects. They are typically evaluated for their values, have expression scope, and *cannot* be assigned to. Because they are expression scope, as soon as assigning a value to them, they would already go out of scope. 

To support move semantics, C++11 introduced 3 new categories: pr-value, x-values, and gl-values, but we don't need to understand these to understand move semantics. 

#### L-value references

Before C++11 this was the only type of reference. Now they are called l-value reference. These can only be initialized with modifiable l-values

| L-value reference | Can be initialized with | Can modify |
| -- | -- | -- |
| Modifiable l-values | Yes | Yes |
| Non-modifiable l-values | No | No |
| R-values | No | No | 

L-value references to const object can be initialized with l-values and r-values alike. However, those values can't be modified. 

| L-value reference to const | Can be initialized with | Can modify |
| -- | -- | -- |
| Modifiable l-values | Yes | No |
| Non-modifiable l-values | Yes | No |
| R-values | Yes | No |

These are particularly useful because they allow us to pass any type of argument without making a copy of the argument. 

#### R-value references

As of C++11 there are now r-value references. These are designed to be initialized with r-values (only). They are created with a double &&.

```cpp
int x{ 5 };
int &lref{ x }; //l-value reference
int &&rref{ 5 }; // r-value reference (cannot use x)
```

They cannot be initialized with l-values

| R-value reference | Can be initialized with | Can modify |
| -- | -- | -- |
| Modifiable l-values | No | No |
| Non-modifiable l-values | No | No |
| R-values | Yes | Yes |

| R-value reference to const | Can be initialized with | Can modify |
| -- | -- | -- |
| Modifiable l-values | No | No | 
| Non-modifiable l-values | No | No |
| R-values | Yes | No |


Just like l-values references to const objects, r-value references extend the lifespan of the object they are initialized with to the lifespan of the r-value reference. They also allow you to modify they r-value!

```cpp
auto &lref{ Fraction{ 3, 5 } }; // this won't compile because fraction is temp
auto const &lref{ Fraction{ 3, 5 } }; // this will compile but you can't change fraction because it only exists as a temp value. Note that this also extends the lifespane of Fraction.
auto &&rref{ Fraction{ 3, 5 } }; // this will compile and you can change the fraction and no copies are created. 
```

R-values are almost never used in this way, they are most often used as function parameters for overloads when you want to have different behaviour for l-value and r-value arguments. 

```cpp
void fun(const int &lref){
    std::cout << "l-value \n";
}

void fun(int &&rref){
    std::cout << "r-value \n";
}
```

## Move constructors and move assignments

#### Copy Semantics Review

Copy assignment is used to copy one class to another existing class. These constructors are provided by default for all classes unless the user specifies one. These copies do shallow copies, which may cause problems for classes that allocate dynamic memory, so classes that deal with dynamic memory should override these functions to do deep copies. Let's go back to our Auto_ptr class to see how this works.

```cpp
template<class T>
class Auto_ptr3
{
	T* m_ptr;
public:
	Auto_ptr3(T* ptr = nullptr)
		:m_ptr(ptr)
	{
	}

	~Auto_ptr3()
	{
		delete m_ptr;
	}

	// Copy constructor
	// Do deep copy of a.m_ptr to m_ptr
	Auto_ptr3(const Auto_ptr3& a)
	{
		m_ptr = new T;
		*m_ptr = *a.m_ptr;
	}

	// Copy assignment
	// Do deep copy of a.m_ptr to m_ptr
	Auto_ptr3& operator=(const Auto_ptr3& a)
	{
		// Self-assignment detection
		if (&a == this)
			return *this;

		// Release any resource we're holding
		delete m_ptr;

		// Copy the resource
		m_ptr = new T;
		*m_ptr = *a.m_ptr;

		return *this;
	}

	T& operator*() const { return *m_ptr; }
	T* operator->() const { return m_ptr; }
	bool isNull() const { return m_ptr == nullptr; }
};
```

This still makes for a lot of resource creation and destruction. Every time the pointer is created or returned it does a deep copy of everything. This doesn't crash, but it's quite inefficient! Move semantics fixes this. Here is the same class using r-value references:

```cpp
template<class T>
class Auto_ptr4
{
	T* m_ptr;
public:
	Auto_ptr4(T* ptr = nullptr)
		:m_ptr(ptr)
	{
	}

	~Auto_ptr4()
	{
		delete m_ptr;
	}

	// Move constructor
	// Transfer ownership of a.m_ptr to m_ptr
	Auto_ptr4(Auto_ptr4&& a) noexcept
		: m_ptr(a.m_ptr)
	{
		a.m_ptr = nullptr; // we'll talk more about this line below
	}

	// Move assignment
	// Transfer ownership of a.m_ptr to m_ptr
	Auto_ptr4& operator=(Auto_ptr4&& a) noexcept
	{
		// Self-assignment detection
		if (&a == this)
			return *this;

		// Release any resource we're holding
		delete m_ptr;

		// Transfer ownership of a.m_ptr to m_ptr
		m_ptr = a.m_ptr;
		a.m_ptr = nullptr; // we'll talk more about this line below

		return *this;
	}

	T& operator*() const { return *m_ptr; }
	T* operator->() const { return m_ptr; }
	bool isNull() const { return m_ptr == nullptr; }
};
```

Now instead of creating and copying and destroying on assignment, the old pointer is set to null and the new pointer is pointed at the value. Moving the value makes it so that the pointer can be copied all over the place without the actual value changing. 

!!! note
    If you want a move constructor and move assignment that do moves, you'll need to write them yourself. Only in rare cases where all other constructors are obstructed will cpp auto-create one. 

### Key Insight

If we do assignment or construct with an l-value, the only thing we can do is copy the l-value. We can't assume it's safe to alter the l-value, because it may be used again later in the program. a=b should never change b. 

If we do assignment or construction with an r-value, then we know that this is just a temp object anyway. Instead of copying this, we can then steal the resources from it to the object we're constructing or assignment. This is because that value will be discarded anyway!

Because r-value references have a definition as of c++11, we can overload our constructor to use this more efficient method when r-values are provided to it.

!!! note
    You should always re-assign your temp object to nullptr otherwise they will become dangling references. Otherwise when they are deconstructed they will delete the data of the pointer!

!!! note
    Often it's desirable to disable the copy and assignment creator in move-enabled classes. This guides the user to use r-values instead.

## std::move

When you start using move semantics more, you'll often want to move things with l-values. std::move does just this. It assigns an empty value to the variable, and passes the value on as a r-value. 

```cpp
template<class T>
void myswap(T& a, T& b)
{
  T tmp { std::move(a) }; // invokes move constructor
  // a is now an empty string
  a = std::move(b); // invokes move assignment
  b = std::move(tmp); // invokes move assignment
}
```

std::move gives a hint to the compiler that the programmer doesn't need this object anymore in it's current state. Move functions should always leave your objects in a well-defined state so they don't crash your program when they go out of scope. 

This is also very useful when sorting an array of elements. 

# std::move_if_noexcept

The noexcept exception specifier and operator is a guarantee to the compiler that a function or class will not raise any exceptions. 

Consider when we are coping an object, and this copy fails (e.g. the machine is out of memory). This doesn't harm the object being copied, and we move on. When moving, things aren't as simple. If the move operation brakes, then the original object will be empty and we might lose data. To comply with the *strong exception guarantee* that we want for our functions, we'd need to move the resource back to the source object. But there's no guarantee that move will work either.

To counter this we have two options:

* Use noexcept on the constructor to prevent it from throwing an exception.
* Ust std::move_if_noexcept instead. 

!!! note
    std::move_if_noexcept will return a movable r-value if the object has a noexcept move constructor, otherwise it will return a copyable l-value. 

This is great especially for templates where you might not be sure if a class has a noexcept constructor. This noexcept is not a compile-time guarantee, it's still up to the person writing the noexcept functions to wrap things up properly in a try catch block.

The standard library uses noexcept a lot along with move_if_noexcept. It's useful, but not a guarantee. 

# std::unique_ptr

If a function exits early, or throws an exception, there's a chance that your pointers might now get deleted. A smart pointer can offer other features, but the basic functionality of it is that it manages a dynamically allocated resources on the heap, and *ensures* destruction of that resource whenever the pointer goes out of scope.

!!! note
    Never dynamically allocate smart pointers themselves. If the smart pointer is never cleaned up, it will never clean up the object it's pointing to and the whole purpose of it will be invalid.

std::unique_ptr is the C++11 replacement for std::auto_ptr. This should be used to manage any dynamically allocated object that is not shared by multiple pointers. It also properly implements move semantics. 

```cpp
res2 = res1; // Won't compile: copy assignment is disabled
res2 = std::move(res1); // res2 assumes ownership, res1 is set to null
```

Because it's designed with move semantics, copy initialization and copy assignment are disabled. Move semantics are the only way to transfer ownership. 

std::unique_ptr has overloaded * and -> operators that work just like with regular pointers. Before using them you should check that the resource exists. This is easy because the implicit cast to bool return true if it's managing a resources. 

```cpp
std::unique_ptr<Resource> res{ new Resource{} };
if (res) // use implicit cast to bool to ensure res contains a Resource
    std::cout << *res << '\n'; // print the Resource that res is owning
```

std::unique_ptr works well with arrays. 

!!! rule
    Favor std::array, std::vector, or std::string over a smart pointer managing a fixed array, dynamic array, or c-style string.

As of C++14 there is also std::make_unique(), which is the preferred way to create smart pointers. It not only is more readable, but it handles some exceptions that can result form C++ leaving the order of evaluation for function arguments unspecified. 

!!! rule
    use std::make_unique() instead of creating std::unique_ptr and using new yourself. 

```cpp
auto f1{ std::make_unique<object>(arg, arg) };
```

Always return std::unique_ptr by value - the internals will apply the correct move semantics. 

```cpp
std::unique_ptr<Resource> createResource()
{
     return std::make_unique<Resource>();
}
```

Likewise always pass std::unique_ptr by value into functions, and use std::move to put it into the arg. The compiler will force you to use std::move because copy semantics for std::unique_ptr have been disabled. 

```cpp
//    takeOwnership(ptr); // This doesn't work, need to use move semantics
takeOwnership(std::move(ptr)); // ok: use move semantics
```

Even though this is possible, usually you won't want the function to take ownership of the pointer. If it took ownership, then the pointer would be destroyed afterwards. Just pass the resource the pointer is pointing to itself, by pointer or reference. This way the function can remain agnostic as to how the caller is managing resources. You can use get() on a std::unique_ptr to get the resource. In this case you don't want to delete the pointer at the end of the function since the memory will still be in use afterwards.

```cpp
void useResource(Resource *res)
{
	if (res)
		std::cout << *res << '\n';
}

auto ptr{ std::make_unique<Resource>() };
useResource(ptr.get()); // note: get() used here to get a pointer to the Resource
```

You can use std::unique_ptr as part of your classes, but remember that if the class itself is dynamically allocated and not cleaned up using smart pointers won't make a difference. 

Two things to avoid with mart pointers

* Don't let multiple classes manage the same resource. 

```cpp
Resource *res{ new Resource() };
std::unique_ptr<Resource> res1{ res };
std::unique_ptr<Resource> res2{ res };
```

Both res1 and res2 will try to delete the same resource after going out of scope, which will lead to undefined behaviour.

* Don't manually delete the resource out from underneath the std::unique_ptr

```cpp
Resource *res{ new Resource() };
std::unique_ptr<Resource> res1{ res };
delete res;
```

# std::shared_ptr

This is the class for when you want multiple smart pointers pointing at the same resource. 

```cpp
// allocate a Resource object and have it owned by std::shared_ptr
Resource *res = new Resource;
std::shared_ptr<Resource> ptr1(res);
{
    std::shared_ptr<Resource> ptr2(ptr1); // use copy initialization to make another std::shared_ptr pointing to the same thing

    // DO NOT DO IT LIKE THIS
    std::shared_ptr<Resource> ptr2(res); // this will then kill the resource if going out of scope

    std::cout << "Killing one shared pointer\n";
} // ptr2 goes out of scope here, but nothing happens

std::cout << "Killing another shared pointer\n";
```

As you can see above, it's important to create std::shared_ptr that point at the same resources from each other. 

!!! rule
    Always make a copy of an exisiting std::shared_ptr if you need more than one std::shared_ptr pointing to the same resource

#### std::make_shared

Like with std::make_unique, it's better to use std:make_shared. 

```cpp
int main()
{
	// allocate a Resource object and have it owned by std::shared_ptr
	auto ptr1 = std::make_shared<Resource>();
	{
		auto ptr2 = ptr1; // create ptr2 using copy initialization of ptr1

		std::cout << "Killing one shared pointer\n";
	} // ptr2 goes out of scope here, but nothing happens

	std::cout << "Killing another shared pointer\n";

	return 0;
} // ptr1 goes out of scope here, and the allocated Resource is destroyed
```

Internally, std::shared_ptr uses two pointers. One poitns at the resource bing managed, the other at a "control block" which dynamically allocated and tracks a bunch of stuff like how many instances of the shared_ptr there are. 

You can convert std::shared_ptr from std::unique_ptr, but you can't convert a std::stared_ptr into a std::unique_ptr. 

Just like std::unique_ptr you have to worry about the pointer itself being properly disposed of. And not just one, but all instances of the shared_ptr. The resource will be deallocated with the last std::shared_ptr managing the resource is destroyed. 

# Circular dependency issues with std::stared_ptr and std:weak_ptr. 

**Circular references** are a series of references where each object references the next, and the last object references back to the first, creating a loop. They do not need to be actual references, they can be pointers, unique ID's or any other means of identifying specific objects. A points at B, B points at C, C points at A. If these go out of scope like this, none of the objects will be deallocated if they are shared pointers, because there's no place to start deallocating.

The same thing also happens with a std::shared_ptr referencing the object that contains it. 

std::weak_ptr is designed to solve this cyclical ownership problem. It is an observer, it can observe and access the same object as a std::shared_ptr, but is not considered an owner. 

So functionally a std::weak_ptr is the same as a std::shared_ptr, but it doesn't count as a "user" of the object. The annoying thing is to use the ptr you need to convert it to a std::shared_ptr first. 


```cpp
#include <iostream>
#include <memory> // for std::shared_ptr and std::weak_ptr
#include <string>
 
class Person
{
	std::string m_name;
	std::weak_ptr<Person> m_partner; // note: This is now a std::weak_ptr
 
public:
 
	Person(const std::string &name) : m_name(name)
	{
		std::cout << m_name << " created\n";
	}
	~Person()
	{
		std::cout << m_name << " destroyed\n";
	}
 
	friend bool partnerUp(std::shared_ptr<Person> &p1, std::shared_ptr<Person> &p2)
	{
		if (!p1 || !p2)
			return false;
 
		p1->m_partner = p2;
		p2->m_partner = p1;
 
		std::cout << p1->m_name << " is now partnered with " << p2->m_name << "\n";
 
		return true;
	}
 
	const std::shared_ptr<Person> getPartner() const { return m_partner.lock(); } // use lock() to convert weak_ptr to shared_ptr
	const std::string& getName() const { return m_name; }
};
 
int main()
{
	auto lucy = std::make_shared<Person>("Lucy");
	auto ricky = std::make_shared<Person>("Ricky");
 
	partnerUp(lucy, ricky);
 
	auto partner = ricky->getPartner(); // get shared_ptr to Ricky's partner
	std::cout << ricky->getName() << "'s partner is: " << partner->getName() << '\n';
 
	return 0;
}
```









