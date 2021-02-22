# std::string

C-style strings have many shortcomings, primarily revolving around the fact that you have to do all the memory management yourself. 

```cpp
char *stdHello = new char[7]; // don't forget to account for an extra null terminator!
strcopy(strHello, "hello!"); // then you have to copy the value in char by char
// Hopefully you made your buffer large enogh so there's no buffer overflow!
// and of course you have to remember to delete it as wel
delete [] strHello; // don't forget to use array deleted 
```

There are 3 different string classes in the <string> header. basic_string is the superclass that string and wstring are derived from. std::string is used for standard ascii and utf-8 strings, and std::wstring is used for utf-16 strings. There is no class for utf-32 strings, but it wouldn't be hard to make one. 

All the functionality is implemented in basic_string, with the only different in string and wstring being their size. There are a couple of things that the std::string does not include:

* RegEx support
* Creating strings from numbers
* Capitalization 
* Case-insensitive comparisons
* Tokenization / splitting into array
* Easy function for getting left or right hand portion of string
* Whitespace trimming
* Formatting the string in sprintf style
* Conversion from utf-8 to utf-16

## std::strnig construction and destruction

```cpp
std::string Source{"myString"}; //default constructor
std::string sOutput{sSource}; // copy constructor
std::string trimmed{sSource, "4"};
const char *szSource{ "my string" };
std::string CStyle { szSource }; //cstyle constructor
std::string CStyleTrimemd { szSource, 3 }; //cstyle constructor of only the first 3 characters
std::string sOutput(4, 'Q'); // create a string of 4 Q chars
```

There is no way to create a string from numbers using <string>. The easiest way to work aroundt his is to use std::ostringstream, which is set up to accept input from a variety of sources. 

```cpp
#include <iostream>
#include <sstream>
#include <string>

template <typename T>
inline std::string ToSTring(T tX){
    std::ostringstream oStream;
    return !(oStream << tX).fail(); //extract value into tX ,return success or not
}
```

You can find the length of a string with 

```cpp
// these two are identical
int len = string.length();
int len = string.size(); 

int max_size = string.max_size() // the maximum amount of characters allowed in a string
```

* Instead of using (s.length() == 0) use s.empty()

You can get the capacity of a string to find out how much memory it has allocated for. Strings generally try to allocate more memory than they need because re-allocation is expensive. If you know your string will need a certain amount in the future, you can use string.reserve() to pre-allocate the string to a certain capacity. 

```cpp
std::srand(std::time(nullptr)); // seed random number generator

string sString{}; // length 0
sString.reserve(64); // reserve 64 characters

// Fill string up with random lower case characters
for (int nCount{ 0 }; nCount < 64; ++nCount)
    sString += 'a' + std::rand() % 26;
```

When accessing a string's characters outside of it's range will lead to undefined behaviour. string.at() will provide an out_of_range exception if this is tried, and is therefore safer. 

You can convert strings into c-style arrays in the different ways

```cpp
std::string myString { "mystring" };
const char *cString {myString.c_str()}; // cString is owned by std::string and should not be deleted
char *cStringData {myString.data()}; // cStringData is owned by std:string and should not be deleted
const char *cStringDataConst {myString.data()}; // same as above

char szBuf[20]:
int nLength{ static_cast<int>(sSource.copy(szBuf, 5, 10)) }; // copy the string to the buffer - it's up to the caller to ensure szBuf is initialized to NULL or terminate the string using the returned length and not overloadin szBuf
szBuf[nLength] = '\0'; // make sure to terminate string in buffer
```

You can assign a value to a string using the overloaded operator= function, or string.assign(). 

```cpp
string sString;
sString  = string("one"); // Assigne a string value
sString = "two"; // Assignea c-style string
sString = '5'; // Assign a char
sString.assign("Four"); // use the assign functions
oString = "Buffallo";
sString.assign(oString, 2, 4); // assign a substring from index 2 to 6 (length 4)
sString.swap(oString); // swap the values of oString and sString
```

Appending strings has several forms as well

```cpp
string sString{"My GREATEST string"};
sString += string(" nope"); // add two string objects 
sString.append(" nope" ); // same as above - both also work on c-style strings
sString.append(" copppiy", 4); // only append the first four chars
sString.pushBack(" nope"); same as append but with stack terminology
```

Inserting strings

```cpp
string sString{ "Hello there" };
sString.insert(5, " my dog"); // insert from index 5 of the string
sString.insert(5, " my dog ate christmas", 0, 6); // produces same result as above by using the substring chars 0-6
sString.insert(5, " my dog ate christmas", 6); // same as above
sString.insert(5, 3, 'i'); // "Helloiii there" 
```










