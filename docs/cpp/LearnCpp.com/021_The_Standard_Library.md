# The Standard Library

The Standark library contains a collection of classes that provide templted containers, algorithms, and iterators. This is a high level look at some of the things in the standard library. 

## STL containers overview

The most used things are the container classes. There are six basic containers:

### std::vector

The unfortunately named **vector** is a dynamic array capable of growing as needed to contain its elements. 

```cpp
std::vector<int> vect;
for (int count=0; count < 6; ++count)
    vect.push_back(10 - count); // insert at end of array
```

### std::deque

Pronounced "deck" this is a double-ended queue class which can grow from both ends. 

```cpp
std::deque<int> deq;
for (int count=0; count < 3; ++count)
{
    deq.push_back(count); // insert at end of array
    deq.push_front(10 - count); // insert at front of array
}
```

### std::list

A special type of sequence container called a doubly linked **list**, wher each element in the container contains pointers that point at the next and previous elements in the list. Only the start and end of the list are provided access to, there is no random access provided. You need to start and one end and "walk the list" until you reach the element you want to find. Inserting elements is super fast if you know where you want to insert them. 

### Associative Containers

These are containers that automatically sort their inputs when inserted to the container. They usually compare elements using <, so the elements will need an overloaded operator with that. 

* **std::set** stores unique elements, with duplicates forbidden. The elements are sorted by their values
* **std::multiset** is a set where duplicate elements are allowed
* **std::map** (aka associative array) is a set where each element is a pair as in a key/value pair. The key is used for sorting and indexing the data. These are like dicts in python
* **std::multimap** (aka dictionary) is a map that allows duplicate keys. All the keys are automatically sorted in ascending order. Some words have multiple values, which is why a dictionary is a multimap rather than a map.

### container Adapters

These are special predefined containers that are adapted to specific uses. 

* **std::stack** is a container where elements operate in a LIFO (Last in, First out) context. As elements are pushed in (inserted), the ones at the end are popped (removed) from the container. 
* **std::queue** is FIFO (First in, First out). Elements are pushed to the back and popped from the ront. 

Both of the above default to using deques, stack can also use vector or list, queue can also use list. 

* **std::priority_queue** is a queue where the elements are kept sorted. The elements that are popped are always the ones of highest priority.

## STL iterators overview.

**Iterators** are objects that iterate over a container class without the user caring about how the container is implemented. Particularly with lists and similar classes, iterators are the primary way to access their elements. It's good to think of them as pointers to a given element in the container, with overloaded operators to provide as set of common functions

* **Operator*** returns the element the iterator is currently pointing at
* **Operator++** moves the iterator tot he next element. (Most also provide operator-- to move to previous element)
* **Operator== and Operator!=** compaer if two iterators point to the same element.
* **Operator=** Assign iterator to new position. This is different than assigning a value to what the iterator is pointing at. To do *that* you need to dereference the iterator first. 
 
There are also four basic member functions for use with Operator=

* **std::begin()** returns iterator pointing at beginning of container's elements
* **std::end()** returns iterator pointing to last of containers elements.
* **std::cbegin()** return const begin (can't change)
* **std::cend()** returns const end to the element after the last element in the container (*not the last*) - this is for more convenient looping.

Each container also provides at least two types of iterators

* **container::iterator** read/write
* **container::const_iterator** read-only iterator

Here's a basic example of using an iterator

```cpp
std::vector<int> vect;
for (int count=0; count < 6; ++count)
    vect.push_back(count);

std::vector<int>::const_iterator it; // declare a read-only iterator
it = vect.cbegin(); // assign it to the start of the vector
while (it != vect.cend()){ // while it hasn't reach the end
    std::cout << *it << " "; // print the value of the element it points to
    ++it; // and iterate to the next element
}
```

This iteration code is essentially identical for all containers. 

## STL algorithms overview

These are algorithms for working with the elements of container classes. They are implements as functions that operator using iterators. In that way each algorithm only needs to be implemented once, and works with all container classes. Some combinations of algorithm + container won't work though, so you still have to think for yourself. 

## std::min_element and std::max_element

```cpp
std::list<int> li(6);
// Fill li with numbers starting at 0.
// iota produces a contiguous series of values
std::iota(li.begin(), li.end(), 0);

std::cout << *std::min_element(li.begin(), li.end()) << ' '
          << *std::max_element(li.begin(), li.end()) << '\n';
```

## std::find and list::insert

Find a value in the list class and use list::insert() to add a new value at that point

```cpp
std::list<int> li(6);
std::iota(li.begin(), li.end(), 0);

// Find the value 3 in the list
auto it{ find(li.begin(), li.end(), 3) };

if(it == li.end()){
    std::cout << "3 was not found\n";
}
else{
    // Insert 8 right before 3.
    li.insert(it, 8);

    for (int i : li) // for loop with iterators
        std::cout << i << ' ';
}
```

If the search algorithm doesn't find what it's looking for, it will return the end iterator, so you need to check for this. 

## std::sort and std::reverse

How to sort a vector and reverse it

```cpp
std::vector<int> vect{ 7, -3, 6, 2, -5, 0, 4 };

// sort the vector
std::sort(vect.begin(), vect.end());

for (int i : vect)
{
    std::cout << i << ' ';
}

std::cout << '\n';

// reverse the vector
std::reverse(vect.begin(), vect.end());

for (int i : vect)
{
    std::cout << i << ' ';
}
```

You can also pass a comparison function as the third argument for std::sort (remember lambdas? also in the <functional> header)
This doesn't work on list container classes, but they provide their own sort() member function. 




