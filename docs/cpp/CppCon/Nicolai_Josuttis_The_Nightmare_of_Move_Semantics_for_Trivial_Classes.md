# The Nightmare of Move Semantics for Trivial Classes

<iframe width="560" height="315" src="https://www.youtube.com/embed/PNRju6_yn3o" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


notes on a talk by Nicolai Josuttis

This is about the problems that a naive programmer has when trying to implement a simple class with two strings. 

```cpp
class cust{
private:
    std::string first;
    std::string last;
    int         id;
}
```

What this class needs is a constructor. This entire talk is about that constructor. 

```cpp
public: 
    Cust(const std::string & f, const std::string & 1 = "", int i = 0)
        : first(f), last(l), id(i) {

    }
```

The problem with this constructor isn't obvious at first. We do it how we learned it. We take a const string reference because we don't want to copy the string. So we take it by reference because copying is expensive. The first question for the morning is the following:

## How many expensive strnig calls are done here?
* Potential memory allocations
- mallocs if no SSO is used
* I.e., copy constructors or copy assignements for std::string

Say we want to initialize like this

```cpp
Cust c{"Joe", "Fix", 42};
```

The arguments expect a string, so we have to craete two strings. Assuming these strings can be over 15 characters so there is no short string optimization. This is our first malloc. 

Now when they enter the constructor, since they are const reference, they are copied again to creat our final object. Now the initial f and l will get destructed. This means we have 4 mallocs: 2 create + 2 copy. 

Before c++ 11 we would do this by overloading the constructor with const char* inputs. This allows us to avoid the 2 copy mallocs. But this breaks down when you try to mix std::strings with inlines. So we have to overload for all four functions

```cpp
class Cust{
private:
    std::string first;
    std::string last;
    int         id;
public:
    Cust(const std::string& f, const std::string& l = "", int i = 0)
        :first{f}, last{l}, id{i} {

        }
    Cust(const char* f, const char* l = "", int i = 0)
        :first{f}, last{l}, id{i} {

        }
    Cust(const std::string& f, const char* l = "", int i = 0)
        :first{f}, last{l}, id{i} {

        }
    Cust(const char* f, const std::string& l = "", int i = 0)
        :first{f}, last{l}, id{i} {

        }
}
```

This is what we need to do if we want to make it perfect before cpp 11. But now we have move semantics. This still has the same amount of copies as just inputting the string, because there's no support for std::move in the constructor. So let's overload the constructor for move semantics

```cpp
public:
    Cust(std::string&& f, std::string&& l ="", int i = 0)
    :first(std::move(f)), last(std::move(l)), id(i) {

    }
```

Now what happens when we initialize the string, is that we steal the heap memory of "Joe" and the heap memory of "Fix". This means two mallocs only. This is because we still need to create temps, but copying them got cheap since we are stealing the memory from the created temps. Again with this we need all the overloads for all the combinations. 

```cpp
public:
    Cust(std::string& f, std::string& l ="", int i = 0)
    :first(std::move(f)), last(std::move(l)), id(i) {
    }
    Cust(std::string&& f, std::string&& l ="", int i = 0)
    :first(std::move(f)), last(std::move(l)), id(i) {
    }
    Cust(std::string&& f, std::string& l ="", int i = 0)
    :first(std::move(f)), last(std::move(l)), id(i) {
    }
    Cust(std::string& f, std::string&& l ="", int i = 0)
    :first(std::move(f)), last(std::move(l)), id(i) {
    }
```

This is great and things work! But wait... what happens when you pass only one argument? 

```cpp
Cust f{"nico"}; // Error: ambigious
```

It doesn't compile. The problem is that we now have two matching constructors. So what you can do now is remove some default values. 


```cpp
public:
    Cust(std::string& f, std::string& l, int i = 0)
    :first(std::move(f)), last(std::move(l)), id(i) {
    }
    Cust(std::string&& f, std::string&& l ="", int i = 0)
    :first(std::move(f)), last(std::move(l)), id(i) {
    }
    Cust(std::string&& f, std::string& l ="", int i = 0)
    :first(std::move(f)), last(std::move(l)), id(i) {
    }
    Cust(std::string& f, std::string&& l, int i = 0)
    :first(std::move(f)), last(std::move(l)), id(i) {
    }
```

Now that previous error will compile. Let's try anothing this: copy intialization:

```cpp
cust g = "nico"; //ERROR: two implicit user-defined onversions
```

This hasn't worked all along. Also the following example has never compiled in c++

```cpp
struct S{
    S(std::string s);
};
S x = "hi"; // ERROR
```

So this means we need to bring back our const * overloads. Technically we only need to overload three more because copy initiliazation is only possible with a single argument. But that would be too ambiguous so let's just do all of them.

```cpp
Cust(const std::string& f, const std::string& l, int i = 0);
Cust(const std::string& f, const std::string&& l = "", int i = 0);
Cust(const std::string& f, const char* l, int i = 0);
Cust(const std::string&& f, const std::string& l, int i = 0);
Cust(const std::string&& f, const std::string&& l = "", int i = 0);
Cust(const std::string&& f, const char* l, int i = 0);
Cust(const char* f, const std::string& l, int i = 0);
Cust(const char* f, const std::string&& l = "", int i = 0);
Cust(const char* f, const char* l, int i = 0);
```

Now it works. We should at least talk about taking the arguments by value. Even though they are stings. This method creates 4 mallocs whith copying the temp strings. Now let's pass them by move. 

```cpp
class Cust{
private:
    std::string first;
    std::string last;
    int id;
public:
    Cust(std::string f, std::string l = "", int i = 0)
        : first{std::move(f)}, last{std::move(l)}, id(i) {

    }
    // for copy initialization
    Cust(const char* f)
        : first(f), last(""), id(0) {

    }
}
```

This now only requires 2 mallocs, because we are stealing the memory with std::move. 

Now we can continue using templates, but I haven't learned about that yet, so I'm going to put this on hold to be continued after I have a deeper understanding of template metaprogramming. 







