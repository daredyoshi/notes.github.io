# Exceptions

If a function fails, a common practice is to use the return code to determine if it did so. This could be a bool or commonly -1 if it failed for int return types. This has a few drawbacks - mainly that it's cryptic, and that the return type is not always something that you can set to explicitly false. In some functions -1 is a valid return type, so the user might be unsure if this is an error or expected behaviour. 

What you can do is to put the result of the function into a reference type like so:

```cpp
double divide(int x, int y, bool &success)
{
    if (y == 0)
    {
        success = false;
        return 0.0;
    }
 
    success = true;
    return static_cast<double>(x)/y;
} 
```

This still needs to be checked constantly, and the user of the function needs to remember to check it constantly. This is also not going to work inside of object constructors. What if something goes *catastrophically* wrong in a cosntructor? 

Enter: Exceptions. Exception handling provides a mechanism do decuople handling of errors or other exceptional circumstances from the typical control flow of your code. There are three keywords that are used to implement exceptions in cpp. 

### Throw

A **throw statement** is used to signal that an exception has occurred. This is also called **raising** an exception. You do this with the throw keywoard, followed by a value of any data type you wish to use to signal that an error has occured. 

```cpp
throw -1; // throw a literal integer value
throw ENUM_INVALID_INDEX; // throw an enum value
throw "Can not take square root of negative number"; // throw a literal C-style (const char*) string
throw dX; // throw a double variable that was previously defined
throw MyException("Fatal Error"); // Throw an object of class MyException
```

### Try

How do you *catch* exceptions? Once they are thrown by a function, do you always want your program to stop? The **try block** runs statements that could potentially generate exceptions and catches them without necessarily stopping the program. 

### Catch
After the exceptions have been gathered by the try block, they are handled by the **catch block**. Try and catch blocks work together. A try block detects the exceptions, and the catch block handles them. Here's an example of them all working together:

```cpp
try
{
    // Statements that may throw exceptions you want to handle go here
    throw -1; // here's a trivial example
}
catch (int x)
{
    // Any exceptions of type int thrown within the above try block get sent here
    std::cerr << "We caught an int exception with value: " << x << '\n';
}
catch (double) // no variable name since we don't use the exception itself in the catch block below
{
    // Any exceptions of type double thrown within the above try block get sent here
    std::cerr << "We caught an exception of type double" << '\n';
}
catch (const std::string &str) // catch classes by const reference
{
    // Any exceptions of type std::string thrown within the above try block get sent here
    std::cerr << "We caught an exception of type std::string" << '\n';
}

std::cout << "Continuing on our merry way\n";
```

After an exception is thrown in a try block, the rest of the block is ignored and the program jumps straight to the catch block. Typically if an exception is routed to a catch block, it's considered handled. These are three common things that catch blocks do:

* print an error (to console or log)
* return an error code back to caller
* throw another exception for the next catch block to handle

## Exceptions with Functions

Exceptions thrown within functions get passed up the stack until they are detected in a try block. The process of tracing them up the stack is called **unwinding the stack**. The interesting part is that you can throw exceptions in a valid way in functions without the code being directly in a try block. This is helpful because different applications my want to handle the exception in different ways. 

Note that program execution is suspended all the way up the stack until the exception is caught. This is very great for skipping out on code dependant on a certain variable being a certain way. 

## Uncaught exceptions and catch-all handlers

If an exception is not caught your program will terminate.... often unexpectedly. This is something you want to avoid. To deal with unknown exceptions completely crashing your program, you can use a **catch-all handler**. It's done, quit simple, by using ellipsis. 

```cpp
catch (...){// catch all handler
    std:cout << "We caught an exception of undetermined type\n";
} 
```

Often this is left empty to ignore all uncaught exceptions. It will keep the stack unwinding to the top of your program, but not do any specific error handling. This is a common use of the catch all handler:

```cpp
int main()
{

    try
    {
        runGame();
    }
    catch(...)
    {
        std::cerr << "Abnormal termination\n";
    }

    saveState(); // Save user's game
    return 1;
}
```

## Exception, classes and inheritance

Because the type of exception can be ambiguous, it makes sense to throw **exception classes**. These are a standardized class that you can assume will be thrown. Any class can be an exception class, but it makes sense to have them store an error string like so :

```cpp
class ArrayException
{
private:
    std::string m_error;

public:
    ArrayException(std::string error)
        : m_error{error}
    {
    }

    const char* getError() const { return m_error.c_str(); }
};
```

!!! note
    Exception handlers should catch class exception object by reference instead of by value. This prevents the compiler from making a copy of the exception.

Exception handler will not only catch classes of a type, but also all classes derived from that type. Remember to catch the exceptions in the reverse order or class inheritance if you do so. 

```cpp
try
{
    throw Derived();
}
catch (const Base &base)
{
    std::cerr << "caught Base";
}
catch (const Derived &derived)
{
    std::cerr << "caught Derived";
}
```

The above will print "caught Base". To get it to catch derived properly the derived catch must be before the base catch. 

Writing your own exception class kind of just adds more exception standards, so usually you will be using or inheriting from std::exception. It's a small class designed to serve as a base to any exception thrown by the standard library. Thanks to std::exception, we can set up an exception handler to catch all exceptions in the standard library!

```cpp
#include <exception> // for std::exception
#include <limits>
#include <string> // for this example

int main()
{
    try
    {
        // Your code using standard library goes here
        // We'll trigger one of these exceptions intentionally for the sake of the example
        std::string s;
        s.resize(std::numeric_limits<std::size_t>::max()); // will trigger a std::length_error or allocation exception
    }
    // This handler will catch std::exception and all the derived exceptions too
    catch (const std::exception& exception)
    {
        std::cerr << "Standard exception: " << exception.what() << '\n';
    }

    return 0;
}
```

Interesting to note is that std::exception has a what() function that returns a C-style string description of the exception. If subclassing, this is what you want to overwrite to add the description for your own exception. 

Using inheritance we can use specific handlers to target specific derived exception classes. 

!!! note 
    Nothing throws a std::exception directly, and neighter should you. A list of standard exceptions can be found [here](http://en.cppreference.com/w/cpp/error/exception)


## Re-throwing exceptions

Sometimes you want to pass an exception up the stack. You can do this by throwing another exception in your catch block. 

```cpp
int getIntValueFromDatabase(Database *d, std::string table, std::string key)
{
    assert(d);
 
    try
    {
        return d->getIntValue(table, key); // throws int exception on failure
    }
    catch (int exception)
    {
        // Write an error to some global logfile
        g_log.logError("doSomethingImportant failed");
 
        throw 'q'; // throw char exception 'q' up the stack to be handled by caller of getIntValueFromDatabase()
    }
}
```

What you should not do is to re-throw the same exception like this. 

```cpp
int getIntValueFromDatabase(Database *d, std::string table, std::string key)
{
    assert(d);

    try
    {
        return d->getIntValue(table, key); // throws Derived exception on failure
    }
    catch (Base &exception)
    {
        // Write an error to some global logfile
        g_log.logError("doSomethingImportant failed");

        throw exception; // Danger: this throws a Base object, not a Derived object
    }
}
```

This is slicing the derived object that's being returned! The right way to re-throw the exception is to use the throw keyword again. 

```cpp
try
{
    throw Derived{};
}
catch (Base& b)
{
    std::cout << "Caught Base b, which is actually a ";
    b.print();
    std::cout << "\n";
    throw; // note: We're now rethrowing the object here
}
```

## Function Try blocks

What if you want to catch an expression within a constructor? For this you need a **function try block**

```cpp
class B : public A
{
public:
	B(int x) try : A(x) // note addition of try keyword here
	{
	}
	catch (...) // note this is at same level of indentation as the function itself
	{
                // Exceptions from member initializer list or constructor body are caught here

                std::cerr << "Exception caught\n";

                // If an exception isn't explicitly thrown here, the current exception will be implicitly rethrown
	}
};
```

These try blocks can catch both base and current class exceptions. Don't try to use these to clean up resources, after the constructor has failed the object is considered undefined behaviour. 

## Exception dangers and downsides

One of the biggest problems with exceptions is manually allocated data. In this case the catch needs to clean up the data or it will leak. What are some ways to do this for pointers that would otherwise go out of scope? One way is to declare the pointer outside of the try block. Another is to use a **std::unique_ptr**. This will automatically deallocate the pointer when going out of scope. 

!!! note
    Exceptions should *never* be thrown in destructors. 
    Write a message to a log file instead.


When should you use exceptions?
    * The error being handles is likely to occur only infrequently (performance consideration)
    * The error is serious and execution could not continue otherwise
    * The error cannot be handled at the place where it occurs. 
    * There isn't a good alternative way to return an error code back to the caller

## Exception specifications and noexcept. 

How can you tell if a function is going to be throwing an exception or not?
 All functions in C++ are classified as either **non-throwing** or **potentially throwing**. Exception specifications are a language mechanism that was originally designed to document what kind of exceptions a function might throw as a part of a function specification. If you want to specifically defined your function as not throwing exceptions, you use the **noexcept specifier**

```cpp
void dosomething() noexcept;// this function is non-throwing
```

The following functions are non-throwing by default

* default constructors
* copy constructors
* move constructors
* destructors
* copy assignment operators 
* move assignment operators

You should still tag them as noexcept if you want to specify them as non-throwing, because they might be calling other functions which might be throwing. You can know if a function is throwing or not by using noexcept.

```cpp
void foo() {throw -1;}
void boo() {};
void goo() noexcept {};
struct S{};

constexpr bool b1{ noexcept(5 + 3) }; // true; ints are non-throwing
constexpr bool b2{ noexcept(foo()) }; // false; foo() throws an exception
constexpr bool b3{ noexcept(boo()) }; // false; boo() is implicitly noexcept(false)
constexpr bool b4{ noexcept(goo()) }; // true; goo() is explicitly noexcept(true)
constexpr bool b5{ noexcept(S{}) };   // true; a struct's default constructor is noexcept by default
```

Exception safety guarantees are contractual guidelines about how functions will behave in the event an exception occurs. There are four levels of safety. 

* No guarantee -- anything could happen
* Basic guarantee -- if an exception is thrown, no memory will be leaked and the object is still usable, but the program may be left in a modified state.
* Strong guarantee -- If an exception is thrown, no memory will be leaked and the program state will not be changed. This means the function must either completely succeed or have no side effects if it fails. This is easy if the failure happens before anything is modified in the first place, but can also be achieved by rolling back any changes so the program is return to the pre-failure state. 
* No throw/ No fail -- the function will always succeed or fail without throwing an exception. 

For example, all destructors should have a no-throw guarantee, as well as all functions that destructors might call. 

!!! note
    Use the noexcep specifier in specific cases where you want to express a no-fail or no-throw guarantee. 
    Better to err on the side of caution and not mark it with noexcept. 

Why should functions be marked as non-throwing?

* They can safely be used in destructors and other dangerous contexts
* They can enable the compiler to perform some optimizations that would not otherwise be available. 
* Some libraries are optimized for noexcept. 












