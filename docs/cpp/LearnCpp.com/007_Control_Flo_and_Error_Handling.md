# Control Flow and Error Handling

A **straig-line program** is a program that takes the same path every time it is run. This is not practical when requesting input from a user. For this, C++ provides a number of **control flow statements**, which allow the programmer to change the normal path of execution. When a control flow statement causes the point of execution to change to a non-sequential statement this is called **branching**. 

## If statements and blocks

The most basic conditional statement. Else is optional. Generally you want to always block if statements, or keep them in one-liners for readability. Otherwise you might run into a **dangling else** problem where you're not quite sure which if an else belongs to. 

Also be careful not to terminate your if statements with semicolons because that will immediately end them. 

## Switch statements

Instead of chaining a bunch of if statements together, it can be more readable to use a switch statement. This is also more efficient because the conditional is evaluated only once. 

```cpp
switch (x){
    case 1:
        std::cout << "One";
        return;
    case 2:
        std::cout << "Two";
        return;
    case 3:
        std::cout << "Three";
        return;
    default:
        std::cout << "Unknown";
        return;
        
}
```

These are preferred both computationally and visually. Each label in the switch statement is called a **case label** . The **default label** or **default case** is used when the switch statement is evaluated to a value that does not have a case label. This should be placed last in the switch block. Instead of returning the entire function, you can also use a **break statement** to continue running the function after executing the statement block. If you do not have a break or return statement, the switch will continue evaluating until it finds a matching case statement like the default case statement. This is called **fallthrough** and is usually not intended. If you do intend it, you should use the [[fallthrough]]; attribute to explicitly call the fallthrough. 

If you would like a single staement block to be used for several case statements this can be done very elegantly:

```cpp
bool isVowel(charc){
    switch ( c ){
        case 'a':
        case 'e':
        case 'i':
        case 'o':
        case 'u':
            return true;
        default:
            return false;
    }
}
```

If you want to defined variables inside a case statement, do so in a block inside the case or before the switch. 

## Goto statements

The unconditional jump casus execution to jump to another spon in the code. This requires a **goto statement** and a **statement label**. Because this quickly leads to **spaghetti code** goto statements are to be avoided. 

## Loops

Each time a loop exectutes, it is called an **iteration**

The simplest loops is the **while statement**:
```cpp
while (condition)
    statement;
```
They are dangerous because they can quicly lead to an infinite loop whter the condition is never met. If you intend for a while loop to go indefinitly use while(true). If you are counting your variables in a while loop, be sure to use signed ints. (otherwise they will overflow when they reach zero).

A **do while statement** is a looping construct that is like a while loop, except the statement always executes at least once. Basically it checks the statement *after* the first iteration. 
```cpp
do{
    selected = std::cin >> selection;
}
while(selected != 1 && selection != 2)
```
This is useful for things like selection where you want to get a selected number first, but you would favor while loops over do-while loops when given an equal choice for readability. 

The **for loop** is preferred when we have an obvious loop variable. There are two types of for loops, the classic for statement and the range-based for statement. The range based for statement uses iterators, and that is not covered at this point in the learning. 
```cpp
for (int count { 1 }; count <=10; ++count){
    std::cout << count << ' ';
}
```

You can achieve the same result easily with a while loop, but the for loop is considered more readable. One cool thing you can also do is iterate over multiple variables with a for loop:
```cpp
for (int x {0}, y{ 9 }; x < 10; ++x, --y)
    std::cout << x << ' ' << y << '\n'
```
This is an acceptable way of working in a for loop. 

## Break and continue

The **break statement** causes a while loop, do-while loop, for loop, or switch statemetn to end, with execution continuing with the next statement after the loop. It's different than return, because it does not exit the function. The **continue statement** is similar, but continues iterating the loop. These statements can make the flow harder to follow, so they should be used primarily when they simplify the loop logic. 

There is also debate about using **early returns** which are not at the end of the function. It's similar to using continue and break, use it when they simplify the functions' logic. 

## Halts

A **halt** is a flow control statement that terminates the program. The std::exit() function is a halt that causes your program to terminate normally. **Normal termination** means that the program is exiting the expected way. Note that the std::exit() function does not clean up local variables in the current function or call stake. To help with this there is the std::atexit() function, which lets you specify functions to run whenever the program exits. 

The std::abort() function causes **abnormal terminations** which is a way of terminating due to an error. 

You should only use a halt if there is no safe way to return normally from the main function. If you haven't disabled exceptions, prefer using exceptions for handling errors safely?

## Testing your code

**Scope creep** happens when you add new capabilities that were originally not planned for. **Software verification/testing** is the process of determining whether or not the software still works for all of the cases. It's important to test your programs in small pieces. Write your program in smalle, well defined units (functions or classes), compile often, and test your code as you go. As you get more advanced there are also many **unit testing frameworks** you can use in C++ that help with a testing framework. Once each of your units has been tested in isolatino they can be integrated into your program, and to make sure this happens correctly you can create **integration tests**. 

## Code coverage
**Code coverage** is a term used to describe how much of the source code of a program is executed while testing. **Statement coverage** refers to the percentage of statements in your code that have been exercised by your testign routines. While aiming for 100% statement coverage is fine, it's not enough to ensure correctness. **Branch coverage** refers to the percentage of branches that have been executed, each possible branch counted separately. (i.e. an if statement has 2 branches. ) You should aim for 100% branch coverage of your code. 

**Loop coverage (0, 1, 2, test)** is the concept that if you have a loop in your code you should ensure it works properly when it iterates 0 times, 1, time, and 2 times. Any value greater than 2 should then also work. 

You also need to consider what tests make sense for functions that take different categories of input. 

## Detecting and handling errors

Many new programmers write code and then only test the **happy path**: only the cases where there are no errors. Remember that **defensive programming** is the practice of trying to anticipate all of thee ways software can be misused, either by end-users, or by developers. Test for the edge cases. 

If one of your functions encounters and error there are two ways to respond:

* continue re-trying the function. This is useful for things outside of the error's control (like network connectivity)
* Throw an exception. 

A good example is a division function: what should the function do if presented with an input of 0? This could be solved like this

```cpp
void printDivision(int x, int y){
    if (y != 0){
        std::cout << static_cast<double>(x) / y;
    }
    else{
        std::cerr << "Error: Could not divide by zero \n"
    }
}```

This also makes it much easier for future programmers to see where an error is coming up. 

For really terrible errors, you can use a halt statement. More on exceptions in a later chapter. 

#### Handling intput data

A **buffer** is a piece of memory set aside for storing data temporarily while it's moved from one place to another. **Input valication** is the process of checking whether user input conforms to what the program is expecting. This can be done inline (as the user types), or post-entry (after the user types). There are 4 main areas where extraction from a buffer might fail

* Input extractino succeeds but the input in meaningless to the program (e.g. entering 'k' as your mathematical operators)
* Input extraction succeeds but the suer enters additional input which stays in the buffer
* Input extraction fails (trying to enter 'q' into a numeric input)
* Input extraction succeeds but the user overflows a numeric value. 

As you write programs, consider how they could fail. 

## Asserts and static_assert

A **precondition** is any condition that is assumed by the programmer and necessary for their code to run. An **invariant** is a condition that must be true while some component is executing, and a **postconditio** is something that must be true after the execution of some component of code. 

A shortcut method to using a conditional statement to detect an invalid paraemter is the **assertion** expression. It will be true unless there is a bug in the program. Asserts are implemented via the **assert* preprocessor macro which lives in the <cassert> header. 
```cpp
assert(gravity > 0.0 && "Gravity cannot be less than zero")"
```

Many developers prefer that asserts are only in debug builds. This can be done by using the macro NDEBUG, which if defined disables the assert macro. Use asserts to document your assumtions and cases that should be logically impossible. 

There is also a **static_assert** which is checked at compile-time rather than at tuntime. The condition for a static asser muse be able to be evaluated at compiletime. It can even be placed in global space.

```cpp
static_assert(gravity>0.0, "Code in this file assumes gravity cannot be negative");
```















