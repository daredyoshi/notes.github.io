# Operator Overloading

In C++, operators are implemented as functions. By using function overloading on the operator functions, you can define your own version of the operators that works with different data types. This is called **operator overloading**. 

To visualize how operators are functions consider:

```cpp
x + y; // the readable format
operator+(x, y); // what's actually going on. 
```

User defined classes have no operators by default, and the compiler will error if you try it. If you want to use operators with your user defined classes, you will need to tell the compiler how the operators should work with two operands of your object type. The following two rules are what the compiler uses when evaluating an expression containing an operator:

* If *all* the operands are fundamental data types, the compiler will call a build-int routine if one exists, or produce error. 

* if *any* of the operands are user data types, the compiler searches for a matching overloaded operator function to call. If it can't find one, if twill try to convert the data types into fundamental data types so it can use a matching built-in operator. If that fails, it will produce a compiler error. 

#### Limitations

* Almost any existing operator in C++ can be overloaded except for conditional(?:), sizeof, scope(::), member selector (.), member pointer selector (.*), typeid, and casting operators. 
* You cannot create new operators or rename existing ones. 
* One of the operators being overloaded must be a user-defined type. 
* It's not possible to change the number of operands an operator supports. 
* All operators keep their default precedence and associativity. 

!!! note
    When overloading operators, it's best to keep the function of the operators as close to the original intent of the operators as possible

There are three different ways to overload operators:

```cpp
class Cents{
private:
    int m_cents;
public:
    Cents(int cents) { m_cents = cent; }
    int getCents() const { return m_cents; }
}
```


1. Member function
With member function overloading, the object is always on the left hand side of the operand, which becomes the implicit *this object. 

```cpp
\\in class Cents
Cents operator+(int value){
    return Cents(m_cents + value);
}
```


2. Friend function

```cpp
// in class Cents
friend Cents operator+(const Cents &c1, const Cents &c2);
friend Cents operator+(int value, const Cents &c1);

Cents operator+(const Cents &c1, const Cents &c2){
    // we can access m_cents because this is a friend function
    return Cents(c1.m_cents + c2.m_cents);
}

// you can overload for each combination of data types you desire
Cents operator+(int value, const Cents &c1){
    return { c1.m_cents + value };
}
```

3. Normal function

```cpp
// in class Cents
int getCents() const { return m_cents };

Cents operator+(const Cents &c1, const Cents &c2){
    // without being a friend function we need to use the getter
    return Cents(c1.getCents() + c2.getCents());
}
```

So which method is the *right* method? That depends. Not all operands can be overloaded as a friend/normal function and not all can be member function. Consider the following >> and << operators: these require std::ostream& to be on the left hand side and would not work as member functions because of this. Conversely, the assignment (=), subscript ([]), function call (()), and member selection (->) operators must be overloaded as member functions because the language requires it. 

* If overloading =, [], (), -> operators use member function. 
* If overloading a unary operator, do so as a member function
* if overloading binary operator that does not modify the left operand, do so as a normal function or friend function if required. 
* If overloading binary operator that modifies its left operand where you can't modify the definition of the left operand do so as normal function or friend function if required.
* If overloading a binary operator that modifies its left operand (+=), and you can modify the definition of the left operand, do so as a member function.



!!! note
    Prefer overloading operators as normal functions instead of friends if it's possible to do so without adding additional functions. 

You can overload the << operator to add automatic prints to your objects. 

```cpp
std::ostream& operator<< (std::ostream &out, const Point &point){
    out << "point(" << point.m_x << ", " << point.m_y << ", " << point.m_z << ')'; 
    // return std::ostream so that we can chain calls to operator
    return out;
}
std::cout << point << '\n';
```

In a similar way you can overload the >> operator to accept input for your class:

```cpp
std::istream& operator>> (std::istream &in, Point &point){
    in >> point.m_x;
    in >> point.m_y;
    in >> point.m_z;

    return in;
}
```

Some more fun operators to overload:

* -object (invert the object) MEMBER
* !object (return true if the object is invalid) MEMBER
* +object (return the object USELESS) MEMBER
* object==object (return true objects are equal) NORMAL
* object!=object (return true if objects are unequal) NORMAL
* object>object (not only for numerical comparison - useful for list sorting) NORMAL
* object++ (return the object then increment it) MEMBER
* ++object (increment and return the object) MEMBER 

To distinguish between prefix and postfix incrementation cpp uses a dummy variable

```cpp
//prefix returns the object after incrementation
Object& Object::operator++()
++object;
//post fix returns the object before incrementation 
Object& Object::operator++(int)
object++;
```

* object[0] (return the subobject of index 0) MEMBER

This MUST return a reference because otherwise assignment would not work. 

```cpp
// given that the second element is 6 and you were to return
// a copy of the value instead of a reference the following would
// resolve to
list[2] = 3
6 = 3 // compilier error!
```

Of course this won't work on pointers to objects, because those are pointer objects. Those would resolve to the pointer assuming you are calling an array of objects of that type and return the next garbage value. 

You can also overload [] with non integer inputs. 

```cpp
skeleton[head]
```

* object(var, var, ...) (Allow calling the object as a function) MEMBER

Calling an object like this uses the objects as a **functor**, which is an object that behaves like a function. The advantage over traditional functions is that it can internally store data. 

* static_cast<type>(object) (Determin how the object is cast to a type) MEMBER

This is **user-defined conversion** where you determine how your objects are converted. 

```cpp
class Cents{
private:
    int m_cents;
public:
    operator int() const { return m_cents;}
}

Cents cents{ 7 };
int c{ static_cast<int>(cents) };
```

* thig = object (Assignment operator - when a variable gets intput to the object)

Note that this operator is very different than the copy constructor which initiliazes variables. The copy constructor is used on new objects when they are created, the assignment operator is used to input a new value into an existing object. When creating/modifying an assignment operator you should add a self-assignment check (especially when allocating/deallocating memory)

```cpp
MyString & MyString::operator= (const MyString& str){
    if (this == &str)
        return *this;

    if (m_data) delete[] m_data;

    m_length = str.m_length;

    m_data = new char[str.m_length];

    for (int i = 0; i< str.m_length; ++i)
        m_data[i] = str.m_data[i];

    return *this;
}
```

The self assignment check can be omitted in objects that aren't allocating memory. 

You can prevent the use of an operator by making it private or using the delete keyword. 





!!! note
    Don't defined overloaded operators that don't make sense!

## The Copy constructor

If you do not have one, C++ will create a public copy constructor for you. A **copy constructor** is a special type of constructor used to create a new object as a copy of an existing object.

```cpp
Fraction fiveThirds(5, 3);
Fraction fCopy(fiveThirds); // this words even if there is only an explicit constructor for the above amount of inputs. 
```

Be default a copy constructor uses **memberwise initialization** which means that each member of the copy is initialized directly from the member of the class being copied. 

```cpp
Fraction(const Fraction & fraction) : 
    m_numerator(fraction.m_numerator), m_denominator(fraction.m_denominator){
        std::cout << "copy constructor called\n";
    }
```

You can prevent copies of your class by explicitly making the copy constructor private. The copy constructor is not called for anonymous object (temp objects). This is because the compiler optimizes it away in a process called **elision**. 

## Converting Constructors, explicit, and delete

Cpp will try to convert literals by automatically putting them into constructors. 

```cpp
class Fraction{
    Fraction(int n = 0, int d = 1):
        m_n{n}, m_d{d} {}
}

void printFraction(const Fraction &f){
    std::cout << f;
}

int main(){
    printFraction(6);}
```

This works because it's implicitly converted by the compilier to match the inputs. Constructors eligible for this are called **converting constructors**. As of Cpp11 this is not just constructors taking a single argument, since that is when uniform initialization was introduced. Sometimes this conversion isn't so desirable. For example when Cpp automatically casts chars to ints for a constructor that would also take a string. One way to avoid this is to use the explicit keyword for the constructor:

```cpp
class MyString{
private:
    std::string m_string;
public:
    explicit MyString(int x){
        m_string.resize(x);
    }

    MyString(const char *string){
        m_string = string;
    }
}

printString('x') // now this won't compile because x can't be implicitly cast to an int 
```

In the above case it's still possible to do an explicit conversion to int using static_cast<int>('x'). If we really want to completely disallow 'x' from being converted to a MyString (implicitly or explicitly), is to use the delete keyword. 

```cpp
class Mystring{
private:
    std::string m_string


public:
    MyString(char) = delete // this can never be converted to an int. this input is strictly fobidden.
    
    explicit MyString(int x){
        m_string.resize(x);
    }

    MyString(const char *string){
        m_string.resize(x);
    }
}

printString('x'); // compile error. 

```

## Shallow vs. deep copying

By default when copying variables, C++ uses a **shallow copy** aka memberwise copy. This means it copies each member of the class individually. This works well for simple classes. When dynamically allocating memory this can be a bit dangerous though. The problem is that all pointers are copied, but the data they are pointing towards isn't. 

```cpp
MyString hello{ "Hello, world!" };
{
    MyString copy{ hello }; // use default copy constructor
} // copy is a local variable, so it gets destroyed here.  The destructor deletes copy's string, which leaves hello with a dangling pointer

std::cout << hello.getString() << '\n'; // this will have undefined behavior
```

One answer to this problem is to do a **deep copy** on any non-null pointers being copied. This allocates memory for the copy and then copies the actual value. This requires that we write our own copy constructors and overloaded assignment operators. 

```cpp
void MyString::deepCopy(const MyString& source)
{
    // first we need to deallocate any value that this string is holding!
    delete[] m_data;
 
    // because m_length is not a pointer, we can shallow copy it
    m_length = source.m_length;
 
    // m_data is a pointer, so we need to deep copy it if it is non-null
    if (source.m_data)
    {
        // allocate memory for our copy
        m_data = new char[m_length];
 
        // do the copy
        for (int i{ 0 }; i < m_length; ++i)
            m_data[i] = source.m_data[i];
    }
    else
        m_data = nullptr;
}
 
// Copy constructor
MyString::MyString(const MyString& source)
{
    deepCopy(source);
}

// Assignment operator
MyString& MyString::operator=(const MyString & source)
{
    // check for self-assignment
    if (this != &source)
    {
        // now do the deep copy
        deepCopy(source);
    }
 
    return *this;
}
```

All of this shouldn't be done for any classes that are already in std!






