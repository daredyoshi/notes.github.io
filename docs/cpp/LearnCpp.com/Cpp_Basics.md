# Statements and the structure of a program

Yay we can finally start programming! At the end of this chapter we can write our own basic programs. 

A **statement** is a type of instruction for a program that causes the program to *perform some action*. Statements are like sentences. Most statements end in ;

There are many different kinds of statments in C++:

1. Declaration statements
2. Jump Statements
3. Expression statements
4. Compount statements
5. Selection statements (conditionals)
6. Iteration statements (loops)
7. Try blocks

Statements are usually groupd into **functions**, which are collections of statements that execute sequentially. Every program must have a **main** function, which then starts all other functions. After the main function is finished the program usually terminates. 

Comments in C++ are started with ```//```
Multi-line comments are like this 

```
/*
*/
```

This is identical to vex. 

## Proper use of comments
For each library explain *what* the library does. For each program or function in a library explain *how* the code is going to accomplish the goal, and for statements *what* a statement is doing. 

Comments should be bringing the statement into the larger context, not re-writing what the statement is already saying. Describe why you made certain decisions. Don't ever assume people have any idea what your code is doing. 

In vim my hotkey for commentnig out code is ```<c-//>```

## Intruduction to variables

**Data** is any information that can be moved, processed, or stored by a computer. Programs are collections of instructions that manipulate data to produce a desired result. 

**RAM** (random access memory) can be though of as a series of mailboxes that can be used to hold data while the program is running. Each piece of data is called a **value**. In C++, direct memory access is not allowed. All values must be indirectly accessed through **objects**: regions of storage (usually memory) that have a value and properties. 

Objects that we use are usually named objects called **variables**, the name of which is called an **identifier**. In C++ objects are **NOT** functions. 

Variables are instantiated, by a statement called a **definition**. 

```int x; // define a varible named x of type int```

At **runtime** (when the program is run), that variable will be **instantiated** (the variable object created and assigned to memory address). You cannot use uninstantiated variables! Instantiated objects are called an **instance**.

Each variable must have a **type** which tells the compiler what object class that variable is. Types tell the program how to interpret a value in memory. In C++ the type of a varible must be known at **compile-time** - even if that type is auto. You can convert between types by **casting** the variables. You can also create your own types of course. 

Don't define variables of the same type on a single line. Just because you can doesn't mean you should.

## Variable assignment and initialization

**Assignment** is when you give a varible a value. It is when you copy a value into a variable. ```=``` is the **assignment operator**. When you assigne a new value to a varriable it overwrites the previous value. 

**Initialization** is when you define and assign the variable in a single line. ```int width = 5;```. **Direct initialization** is when you intialize from the value that a function/object returns. 

Generally you should initialize all your variables using **list initialization**. ```int width { 5 }``` This is one of the most general ways of initializing variables. You can leave the brackets empty to initialize to defaults, which is called **value initialization**. ```int width {};``` The great thing about list initialization is that it will throw an error if you are initializing to an incorrect value. 

It might be worth ```int x { 0 }``` if you are explicity going to use that value and not immediately replace it.

Generally you always want to initialize your variables upon creation to be more explicit unless there's a bespoke performance reason not to.

## Introduction to iostream: cout, cin, and endl

**std::cout** throws things into the "character output" and is part of the std (standard) library. The **insertion operator (<<)** is used to send the text to the world. To print multiple lines you can use the << multiple times with **std:endl**.

It's more efficient use use '\n' though. Note that this is a backslash. 

**std::cin >> x;** prompts the user for an input and puts that value into x. Note that the >> arrows are pointing the other way in the direction the data is going.  

Note that this does not replace keyboard input as the user is required to hit enter to confirm their entry. 

## Uninitialized variables and undefined behaviour

C++ doesn't initialize most variables to a default. The default value is whatever garbage is already located at that address! This is called an uninitialized variable. Variables can have a value and be unintialized, so it's not a good idea to test for them having a value. **Always initialize your variables**!

Uninitialized variables is one example of **undefined behaviour (UB)**. This is the result of executing code whoe behaviour is not well defined by the c++ language. The following is a list of possible symptoms of undefined behaviour:

* Different results each time it's run (sys.argv!)
* Incorrect result
* Inconsisten behaviour
* Incorrect results come after a time
* Crashes
* Only works on some compilers
* Changing code in one place has very strange effects in other places

## Keywords and naming identifiers

**Keywords** are words that have a predefined meaning in C++. Don't use these as variable names. 

There are also special identifiers that are not reserved, *override, final import, module*

### Identifier naming rules

* Cannot be keyword
* Can only have letter, numbers and _
* Must begin with letter or underscore (cannot begin with number)
* Case sensitive 

### Identidifer best practices

* lowercase unless user defined struct, class or enumeration
* snake_case or camelCase
* Always match the existing style of code
* don't begin with _
* better to be too long than not descriptive enough
* trivial identifiers (like i in a quick for loop) are ok.
* avoid abbreviations
* Use comments to clarify more information if way too long.

## Whitespace and basic formatting

**Whitespace** referrs to characters that are used for formatting purposes like spaces, tabs, and newlines. C++ compiilers generally ignore this, which means C++ is a whitespace-nidependent language. 

In quoted text they do matter though. You cannot have newline in quoted text. Also single line comments end with a newline character (duh)

Tabs vs. Spaces. I use the Google C++ syle guide with the curly brace on the line becaus I want to see more code without scrolling. Like pep8 try to keep lines under 80 chars in length.

When initializaing many varialbes you can tab them so that they are easier to read. 

## Introduction to literals and operators

A **literal (aka literal constant)** is a fixed values that is hardcoded into your program. An **operation** is a mathemetical calculation involving zero or more input values (called **operands**). The symbol specifying the operation to be performed is called the **operator**. 

In addition to (+, -, *, /, ==, >, < etc.) in C++ we also have (=, <<, >>)

There are three different *arities* of operators in C++
* Unary - act on one operand
* Binary - act on left and right operand
* Ternary - act on three operands (there is only one of these)

You can chain operators together and the result will evaluate them in the order of operations like in maths.

## Introduction to expressions

An **expression** is a combination of literals, variables, operators or explicit function calls that produce a signle output value. An expression is **evaluated** until only a single value, the **result** remains. 

Anywhere you use a single value in c++ you can use an expression instead.

You can make **expression statements** which will compile and then be discarded unless being assigned to a variable. 

