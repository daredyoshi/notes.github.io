# Templates

## Intro

Because C++ is a heavily typed language, it can be really annoying to write the same function and overload for every data type. Wouldn’t it be nice if we could write one version of max() that was able to work with parameters of ANY type?

In C++, **function templates** are functions that serve as a blueprint for creating other similar functions. Instead of using defined types these functions can use **template type parameters** that then allow all the functions required to overload for those parameters to be created by compile time. Creating a function template is not really that hard:

```cpp
template <typename T> // this is the tempalte parameter declaration

T max(T x, T y){
    return (x > y) ? x : y;
}
```

The template "type" declaration is called a **template parameter declaration**. The keyword *template* tell the compiler that what follows is going to be a list of template parameters. Then all parameters get places inside the <>. If the function uses multiple template type parameters, they can be separated by commas.

```cpp
template < typenae T1, typename T2>
```

It's common to see them name "T1" and "T2" or other single capital letter names such as "S". It's also generally good to make the parameters and return types of templated functions const references since it's not possible to assume they are going to be fundamental data types. 

```cpp
template <typename T>
const T& max(const T& x, const T& y){
    return (x > y) ? x : y;
}
```

The main drawbacks of templated functions are an increased compile time, and hard to comprehend compiler errors when they are not working. 

## Function template instances

Not all templates possible are created at compile time - only the ones needed for your program to run. When the compiler encounters a call to a function templates, it creates a **function templates instance**. Then in the future it creates more for combinations of input parameters that it doesn't know yet. 

What if the above template function tries to compare two object that do not have overloaded > operators? This will produce a compile error. Adding an overloaded operator> to the class fixes this.

## Template classes

Templates can be used to generalize things like container classes. That way a container class can contain any type of variables. When creating templated classes you need to declare the template declaration again for each member function defined outside the class

```cpp
#ifndef ARRAY_H
#define ARRAY_H
 
#include <cassert>
 
template <class T>
class Array
{
private:
    int m_length{};
    T *m_data{};
 
public:
 
    Array(int length)
    {
        assert(length > 0);
        m_data = new T[length]{};
        m_length = length;
    }
 
    Array(const Array&) = delete;
    Array& operator=(const Array&) = delete;
 
    ~Array()
    {
        delete[] m_data;
    }
 
    void Erase()
    {
        delete[] m_data;
        // We need to make sure we set m_data to 0 here, otherwise it will
        // be left pointing at deallocated memory!
        m_data = nullptr;
        m_length = 0;
    }
 
    T& operator[](int index)
    {
        assert(index >= 0 && index < m_length);
        return m_data[index];
    }
 
    // templated getLength() function defined below
    int getLength() const; 
};
 
// member functions defined outside the class need their own template declaration
template <class T>
int Array<T>::getLength() const // note class name is Array<T>, not Array
{
  return m_length;
}
 
#endif
```

Now when we use this class note how we need to defined the templated variables within the <>

```cpp
int main()
{
	Array<int> intArray(12);
	Array<double> doubleArray(12);

	for (int count{ 0 }; count < intArray.getLength(); ++count)
	{
		intArray[count] = count;
		doubleArray[count] = count + 0.5;
	}

	for (int count{ intArray.getLength() - 1 }; count >= 0; --count)
		std::cout << intArray[count] << '\t' << doubleArray[count] << '\n';

	return 0;
}
```

Now we finally understand what <> mean next to a class! One thing to note is that separating template classes header from their body doesn't really work. This is because by the time the compiler adds the template function implementation of the body, it has forgotten the template definitions and can't match to the class. 

* If you can put all the code into the header.
* If that is too messy, you can rename .cpp to .inl (stands for inline) and then include .inl from the bottom of the .h header. This is the same as putting all the code into the header but is neater. 
* You can also use a three file approach, where the third file contains *all* of the instantiated classes you need. This file is often called templates.cpp

##Template non-type parameters

Basically templates are functions that create classes and functions with the possibility that the inputs allow for polymorphism. You can also have non-type inputs, that are always the same type, but vary in value. Taking the above array class. This pretty much allows you to skip the constructor for a class that has variables that won't change but need to be initialized. 

```cpp
#include <iostream>

template <class T, int size> // size is the non-type parameter
class StaticArray
{
private:
    // The non-type parameter controls the size of the array
    T m_array[size];

public:
    T* getArray();

    T& operator[](int index)
    {
        return m_array[index];
    }
};

// Showing how a function for a class with a non-type parameter is defined outside of the class
template <class T, int size>
T* StaticArray<T, size>::getArray()
{
    return m_array;
}

int main()
{
    // declare an integer array with room for 12 integers
    StaticArray<int, 12> intArray;

    // Fill it up in order, then print it backwards
    for (int count=0; count < 12; ++count)
        intArray[count] = count;

    for (int count=11; count >= 0; --count)
        std::cout << intArray[count] << " ";
    std::cout << '\n';

    // declare a double buffer with room for 4 doubles
    StaticArray<double, 4> doubleArray;

    for (int count=0; count < 4; ++count)
        doubleArray[count] = 4.4 + 0.1*count;

    for (int count=0; count < 4; ++count)
        std::cout << doubleArray[count] << ' ';

    return 0;
}
```

The basically makes is so that we do not have to dynamically allocate the m_array member variable. For any given instance of the class, size is actually constant. 

## Function template specialization

What if you want special behaviour for when a template is created with a specific type? Say we want specifically double values to output in scientific notation. We can do this with **function template specialization**, which creates a specialized version of the function when called with a specific type. 

```cpp
template <class T>
class Storage
{
private:
    T m_value;
public:
    Storage(T value)
    {
         m_value = value;
    }

    ~Storage()
    {
    }

    void print()
    {
        std::cout << m_value << '\n';
    }
};

// print doubles with scientific notation
template <>
void Storage<double>::print()
{
    std::cout << std::scientific << m_value << '\n';
}
```

In this case you are overriding the print function for one very specific return of the template function. Note how the class name includes the template type. 

## Class template specialization

You can also specialize class templates. Let's specialize the following class for a more efficient memory management of bools. 

```cpp
template <class T>
class Storage8
{
private:
    T m_array[8];
 
public:
    void set(int index, const T &value)
    {
        m_array[index] = value;
    }
 
    const T& get(int index) const
    {
        return m_array[index];
    }
};


template <> // the following is a template class with no templated parameters
class Storage8<bool> // we're specializing Storage8 for bool
{
// What follows is just standard class implementation details
private:
    unsigned char m_data{};

public:
    void set(int index, bool value)
    {
        // Figure out which bit we're setting/unsetting
        // This will put a 1 in the bit we're interested in turning on/off
        auto mask{ 1 << index };

        if (value)  // If we're setting a bit
            m_data |= mask;  // Use bitwise-or to turn that bit on
        else  // if we're turning a bit off
            m_data &= ~mask;  // bitwise-and the inverse mask to turn that bit off
	}

    bool get(int index)
    {
        // Figure out which bit we're getting
        auto mask{ 1 << index };
        // bitwise-and to get the value of the bit we're interested in
        // Then implicit cast to boolean
        return (m_data & mask);
    }
};
```

You can overwrite any part you like. We should try to keep a consistent interface with this, because the whole point of not writing another class is that things "just work". Note that you need to re-write the whole class for this.

If you want to specialize a template with only part of the template defined you can do this. Here we are overloading the print function

```cpp
// overload of print() function for partially specialized StaticArray<char, size>
template <int size> // size is still a templated expression parameter
void print(StaticArray<char, size> &array) // we're explicitly defining type char here
{
	for (int count{ 0 }; count < size; ++count)
		std::cout << array[count];
}
```

As of c++14 partial template specialization can only be used with classes, not template function (which must be fully specialized). 

If you do not want to re-write the whole class for template specialization, you can inherit from a common base class. 

```cpp
#include<iostream>

template <class T, int size> // size is the expression parameter
class StaticArray_Base
{
protected:
	// The expression parameter controls the size of the array
	T m_array[size]{};

public:
	T* getArray() { return m_array; }

	T& operator[](int index)
	{
		return m_array[index];
	}

	void print()
	{
		for (int i{ 0 }; i < size; ++i)
			std::cout << m_array[i];
		std::cout << '\n';
	}

	virtual ~StaticArray_Base() = default;
};

template <class T, int size> // size is the expression parameter
class StaticArray: public StaticArray_Base<T, size>
{
public:
};

template <int size> // size is the expression parameter
class StaticArray<double, size>: public StaticArray_Base<double, size>
{
public:

	void print()
	{
		for (int i{ 0 }; i < size; ++i)
			std::cout << std::scientific << this->m_array[i] << ' ';
// note: The this-> prefix in the above line is needed.
// See https://stackoverflow.com/a/6592617 or https://isocpp.org/wiki/faq/templates#nondependent-name-lookup-members for more info on why.
		std::cout << '\n';
	}
};

int main()
{
	// declare an integer array with room for 6 integers
	StaticArray<int, 6> intArray{};

	// Fill it up in order, then print it
	for (int count{ 0 }; count < 6; ++count)
		intArray[count] = count;

	intArray.print();

	// declare a double buffer with room for 4 doubles
	StaticArray<double, 4> doubleArray{};

	for (int count{ 0 }; count < 4; ++count)
		doubleArray[count] = (4.0 + 0.1 * count);

	doubleArray.print();

	return 0;
}
```

You can also partially specialize a template for only pointer types like this

```cpp
template <typename T>
class Storage<T*>// this is a partial-specialization of Storage that works with pointer types
{
private:
    T* m_value;
public:
    Storage(T* value) // for pointer type T
    {
         // For pointers, we'll do a deep copy
         m_value = new T(*value); // this copies a single value, not an array
    }
 
    ~Storage()
    {
        delete m_value; // so we use scalar delete here, not array delete
    }
 
    void print()
    {
        std::cout << *m_value << '\n';
    }
};

```

This is especially useful to include deletion of all pointers.















