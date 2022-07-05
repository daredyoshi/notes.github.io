# Input and output (IO)

The iostream library uses multiple inheritance. 

![iostream](../iostream.gif)

A **stream** is a sequence of bytes that can be accessed sequentially. It can produce unlimited amounts of data over time. There are two types of streams

* **Input streams** are used to hold input from a data producer, such as a keyboard, file, or network. 
* **Output streams** hold output for a particular data consumer like a moniror, file, or printer. The data will sit in the output stream until it starts getting consumed. 

The istream class is used for input streams, and uses the **extraction operator>>** to remove values from the stream. The ostream class is used fr output streams, and uses the **insertion modifier <<** to put value into the stream. iostream handles both. 

There are four standard streams in cpp

1. **cin** an istream class tied to standard input (keyboard)
2. **cout** an ostream class tied to standard output (keyboard)
3. **cerr** an ostream class tied to the standard error (monitor) providing unbuffered output
4. **clog** an ostream class providing buffered output. 

## istream

A common problem with cin is buffer overflow

```cpp
char buf[10];
std::cin >> buf; // what happens if more than 18 chars get input?
```

You can use a **manipulator** object to modify the stream when applied with the extraction or insertion operators. **setw** can be used to limit the number of character read in from the stream. 

```cpp
#include <iomanip.h>
char buf[10];
std::cin >> std::setw(10) >> buf;
```

Now the program will only read the first 9 character out of the stream (leaving room for th terminator).

If you want to get input including whitespace you can use the **get()** function

```cpp
char ch;
while (std::cin.get(ch))
    std::cout << ch;
```

You can also limit the amount of characters to get

```cpp
char strBuf[11];
std::cin.get(strBuf, 11);
std::cout << strBuf << '\n';
```

Watch out for the fact that get() doesn't read in a newline character. Because of this there is the **getline**.


```cpp
char strBuf[100];
std:cin::getline(strBuf, 100);
std::cout << strBuf << '\n';
std::cout << std::cin.gcount() << " characters were read" << std::endl;
```

A couple more useful istream functions

* **ignore()** discards the first character in the stream
* **ignore(int nCount)** discads the first ncount characters.
* **peek()** allows you to read a character from the stream without removing it from the stream
* **unget()** returns the last character read back into the stream so it can be read again by the next call
* **putback(char ch)** allows you to put a character of your choice back into the stream to be read by the next call. 


## ostream and ios


There are two ways to change formatting options: flags an manipulators. **Flags** are boolean variables that can be on or off. Objects placed in a steram that effect the way things are input and output are **manipulators**. 

```cpp
std::cout.setf(std::ios::showpos); // turn on the std::ios::showpos flag
std::cout << 27 << '\n';
prints +27
```

You can turn on multiple flags using the OR (|)

```cpp
std::cout.setf(std::ios::showpos | std::ios::uppercase);
```

**Format groups** are groups of glags that perform similar formatting objections. 

```cpp
std::cout.setf(std::ios::hex);
std::cout.unsetf(std::ios::dec); // otherwise the output won't be in hex
```

You could also make the flag the only one of the format group visible like so

```cpp
std::cout.setf(std::ios::hex, std::ios::basefield); // hex is the only flag on in the basefield group
```

You can chain flags directly into cout

```
std::cout << std::noboolaplpha << true << " " << false << '\n';
std::cout << std::boolalpha << true << " " << false < '\n';
1 0
true false
```

You can set width with a certain char

```cpp
std::cout << std::setw(10) << left << 12345 << '\n'; // print left justified to a width of 10

```

## Stream classes for strings

The string stream classes provide buffers that are not connected to keyboard or mouse. One of the primary uses of these is to buffer output for display at a later time, or process input line-by-line. There are two ways to get data into a stringstream

```cpp
std::stringstream os;
os << "en garde!" << '\n';
os.str("en garde!");
```

To retrieve values

```cpp
std::stringstream os;

os << "123456 23.0"

std::string strValue;
os >> strValue;

std::string strValue2;
os >> strValue2;

std::string strValue{os.str()};
```

This is very useful for converting strings to numbers and back, since the >> and << operators do this by default. 

```cpp
std::stringstream os;

int nValue {12345};
os << nValue;
std::stringVal{os.str(); }

int nValue;
os >> nValue; // convert back to int
```

You can empty stringstreams by overwriting the string

```cpp
os.str("");
os.str();
os.clear(); // generally a good idea to clear the error flags
```

## Stream states and input validation

There are several state flags in the ios_base class that signal conditions that might occur when using streams.

| Flat | Meaning |
| -- | -- |
| goodbit | Everything is ok |
| badbit | Some kind of fatal error occured | 
| eofbit | the stream has reached the end of a file | 
| failbit | A non-fatal error occured (e.g wrong input) |

Generally you access these flags through ios

| ios::_____ | Meaning| 
| -- | -- |
| good() | returns true if the goodbit is set | 
| bad() | returns true if the badbit is set | 
| eof() | returns true if the eofbit is set | 
| fail() | returns true if the failbit is set | 
| clear() | clears all flags and restores goodbit |
| clear(state) | clears all flags and sets the state to state | 
| rdstate() | returns the currently set flags | 
| setstate(state) | set the state flag to state | 

#### Input Validation

The process of checking whether the user input means some set of criteia is **input validation**. There are generally two types of input validation: string and numeric. This is usually in the form of prompting the user for input and then rejecting it if it's not in the correct type. Numerical validation is usually to make sure the numbers are in the correct range. 

Here are some useful validation functions:

| Function | Meaning | 
| -- | -- |
|std::isalnum(int) | Is a letter or digit | 
|std::isalpha(int) | Is a letter | 
|std::iscntrl(int) | Is a control character | 
|std::isdigit(int) | Is a digit |
|std::isgraph(int) | Is printable char that is not whitespace |
|std::isprint(int) | Is printable char including whitespace | 
|std::ispunct(int) | Is neither alphanumeric or whitespace | 
|std::isspace(int) | Is whitespace | 
|std::isxdigit(int) | I hexadecimal digit (0-9, a-f, A-F) | 

Yuo use it like this:

```cpp
bool isValudName(std::string_view name){
    return std::range::all_of(name, [](char ch)){
        return (std::isalpha(ch) || std::isspace(ch));}
    }
// Before C++20, without ranges
  // return std::all_of(name.begin(), name.end(), [](char ch) {
  //    return (std::isalpha(ch) || std::isspace(ch));
  // });
}
```






