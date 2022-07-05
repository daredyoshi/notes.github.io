# Operators

You can find a reference chart for all cpp operators on this [site](https://www.learncpp.com/cpp-tutorial/operator-precedence-and-associativity/)

An **operation** is a mathematical calculation invloving zero or more input values (called **operands**) that produces a new value.  The operation performed is denoted by an **operator**. 

4 + 2 * 3 has 2 operators. The operator **precedence** determines which get resolves first. Cpp uses normal mathematial precedence rules.

The table on the above site lists the operators in order of precedence. The **associativity** determinds wether to evaluate operators of the same precedence from left to right or right to left. 

Parenthesis have one of the highest precedence levels, so you can use them to determine which parts of your expressions you evaluate first. It's generally good practice to use () to be more explicit even if percedence would do it for you.

## Arithmetic operators

Remember that **unary operators** determine the sign of the number. **Unary minus** returns the operand multiplied by -1, and **unary plus** returns the operator as is. + is really only there to provice symmetry with unary minus. 

### Binary arithmetic operators

```+ 0 * / %```

They take left and right operands. There are two version of the division operator depending on the operatnds: **floating point devision** and **Integer division**. As of C++11 integer division always drops the remainder no matter if above or below 0. 

Don't divide by zero - it will crash your program. 

### Arithmetic assignment operators

= += -= *= /= %=

Just a nice little shorthand.

Modulus is great for tesing if a number divided by another number has a remainder. You could test if two ints are divisible by ```static_cast<i>``` them in a %= and only cast them to double if they have a remainder. 

There is no exponent operator, but you can use std::pow(x, y) for that. 

## Increment/decrementing + side effects 

* ++x Increment and return
* --x Decrement and return
* x++ Copy, increment and return copy
* x-- Copy, decrement and return copy.

Basically the first two modify the x variable, while the second two only return the inrementation. You sould use the first two unless there's a good reason to really need to copy it.

## Comma and conditional operators

The **comma operator** allows you to evaluate multiple expressions where a single expression is allowed. Note that this operator has a really low precedence:
```cpp
z = (a, b) // evaluate (a, b) first to get result of b, then assign that value to z
z = a, b // evaluates as (z = a), b so z get assigned tot he value of a, and be is evaluated and discarded
```
Avoid using the comma operator except within for loops. 
Comma is often used as a seperator and this is not related to the operators. 

The **conditional** operator is kind of like a mini for loop. It's also referred to as the tenery operator. 
```cpp
if (condition)
    statement_1;
else
    statement_2;

(condition) ? expression_1 : expression_2;
```
It can help compact code without losing readability. 
```cpp
larger = (x > y) ? x :y;
```
Always parenthesize the conditional part of the conditional operator, and consier parenthesizing the whole thing as well. Because it evaluates as expression the conditional operator can be used in some places where if/else can not. For example initializing const variables. 

Also note that both data types for the condition must be of the same type. 

Generally you should only use the conditional operator when you use the result adn where it enhances readability. 

## Relational operators and floatnig point comparisons

**Relational operators** let you compare two values. 
```> < >= <= == != ```
Each of these evaluates to a boolean.
Also of course you should only use them if yo uneed to:
```cpp
if (b1== true) // absolutely dumb
if(b1)
```
Be careful using these operators with float values. They are not guaranteed to produce the correct results. Equality is especially troublesome. This is where an **epsion** function comes it. Use something like this if you really need to compare floats:
```cpp
#include <cmath> // std::abs
#include <algorithm> // std::max

// return true if the difference between a and b is within epsilon percent of the larger of a and b
bool approximatelyEqual(double a, double b, double epsilon)
{
    return (std::abs(a - b) <= (std::max(std::abs(a), std::abs(b)) * epsilon));
}
```

## Logical operators
```! && ||```
These allow us to check if multiple conditions are true simultaneously. 

If ! is indeded to work on multiple expressions use parentheses. When mixing && and || use parentheses. 






