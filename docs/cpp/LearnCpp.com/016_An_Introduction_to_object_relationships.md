# An Introduction to object relationships

Objects, as concepts, are connected. A square "is a" shape, a flower "depends-on" bees for pollination, a student is a "member-of" a class. This chapter explores the nuances of the relation types of objects in C++. 

The idea of building complex objects from simpler ones is called **object composition**. Because objects in C++ are built from many smaller objects they are often referred to as **composite types**. 


|--|--|--|--|
| **Property** | **Composition** | **Aggregation** | **Association** | **Dependency**|
| Relationship Type | Whole/part | Whole/part | Otherwise unrelated| Otherwise unrelated | 
|Members can belong to multiple classes | No | Yes | Yes | Yes |
| Members existence managed by class | Yes | No | No | No |
| Directionality | Unidirectional | Unidirectional | Unidirectional or bidirectional | Unidirectional | 
|Relationship verb | Part-of | Has-a | Uses-a| Depends-on |

## Composition

* Typically uses normal member variables
* Can use pointer members if the class handles object allocation/deallocation itself
* Responsible for creation/destruction of parts

This tutorial redefines some terms to aid in explaining them. It will use the term **object composition** when referring to both composition and aggregation, and **composition** when referring to the composition subtype. 

To qualify as a composition and object must have the following relationship

* The member is part of the object
* The member can only belong to one object at a time
* The member has its existence managed by the object
* The member does not know about the existence of the object

Composition is when the object creates the member and then dies with the member. The user of the object doesn't need to concern themselves with the creation of the member. In addition to this, the member is not aware of the object it is a part of. We are calling this a **unidirectional** relationship, because the object knows about the member but the member doesn't know about the object. 

The composition should manage its parts without the user of the composition needing to manage anything. When should one sub-class elements of a composition class? Generally it should be done in a way that keeps everything as simple as possible. Each class should be build to accomplish a single task. That task should be either the storage and manipulation of data, OR the coordination of subclasses: not both. 

## Aggregation

* Typically use pointer or reference members that point to or reference objects that live outside the score of the aggregate class
* Not responsible for creating/destroying parts


To qualify as an **aggregation** a whole object and its part must have the following relationship

* The member is part of the object
* The member can belong to more than one object at a time
* The member does *not* have its existence managed by the object
* The member does not know about the existence of the object.

The difference with composition is that the parts can belong to more than one object at once. In aggregation the members are still variables, but they are usually pointers or references to objects that are managed externally. 

You should always pick the simplest relationship that meets the needs of your program, not what seems right in real life. 

## std::reference_wrapper

If you would like to have lists of references, use this object. std::vector cannot handle references because they have to be initialized and cannot be reassigned. It lives in <functional> 

```cpp
std::string tom{ "Tom" };
std::string berta{ "Berta" };

std::vector<std::reference_wrapper<std::string>> names{ tom, berta };

std::string jim{ "Jim" };

names.push_back(jim);

for (auto name : names)
{
// Use the get() member function to get the referenced string.
name.get() += " Beam";
}

std::cout << jim << '\n'; // Jim Beam
```

## Association

To qualify as an association an object and another object must have the following relationship

* The associated member is otherwise unrelated to the object
* The associated member can belong to more than one object at a time
* The associated member does *not* have its existence managed by the object
* The associated member may or may not know about the existence of the object

Unlike a composition or aggregation, an associated member can be aware of the object that's interacting with it. This can be compared to "uses a", a patient "uses" the services of a doctor, but is not tied to them. Most often association are implemented using pointers. 

When objects have relationships with other objects of the same type, this can be called a **reflexive association**. 

## Dependency 

A **dependency** is a very loose associate where one object requires another object's functionality in order to accomplish a specific task. 

Whenever you <include> you are introducing a dependency to your code to that library or header. Associations are a relationship between two classes at the class level, whereas dependencies can be on any level. 

## Container classes

A **container class** is designed to hold and organize multiple instances of another type. The most common is the std::vector. One way to sort container classes is by aggregation and composition. **Value containers** are compositions that store copies of the objects they are holding. **Reference containers** are aggregations that store pointers or references to other objects. 

Typically you cannot mix the types of objects in a container class. 

A great example can be found [here](https://www.learncpp.com/cpp-tutorial/container-classes/)


## std::initializer_list

This is what the compiler gets when you do this:

```cpp
int array[] {1,2,3,4,5,6,6};
```

By default this won't work with your constructors though. You need to make a constructor that take in an initializer list:

```cpp
IntArray(int length) :
		m_length{ length },
		m_data{ new int[length]{} }
	{
 
	}
 
IntArray(std::initializer_list<int> list) : // allow IntArray to be initialized via list initialization
    IntArray(static_cast<int>(list.size())) // use delegating constructor to set up initial arrayi
{
    int count {0};
    for (auto element : list){
        m_data[count] = element;
        ++count;
    }
}

```

If you provide list construction, it's a good idea to provide list assignment as well. 


