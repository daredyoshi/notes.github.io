# Intro to Binary and Bitwise Operators in C++

<iframe width="560" height="315" src="https://www.youtube.com/embed/KXwRt7og0gI" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


In addition to the mathematical operators that we learn about in school, computers have additional operators that operate directly on bits. What if you want mathematical operators that work on the bit level? That is what bit operators are all about. They apply to a base 2 number system instead of a base 10 number system like we are used to. 

There are 6 main operators: 

* ```<<``` Bit shift left
* ```>>``` Bit shift right
* ```&``` Bitwise AND
* ```|``` Bitwise OR
* ```^``` Btiwise XOR
* ```~``` Not operator (inverting all the bits)

These operators can be overloaded and you can get them to work a lot like functions. E.g. ```std::cout```


## Let's talk about bits and bytes

```cpp
int a = s; // how does memory store this?
```

We have a bunch of transistors and they are either 0 or 1 based on how much energy they have. Because we have only two states, we have to use a base 2 number system **binary**. This means that if we want to store a number larger than one we need an additional character. This is how you would count up to 5:

0. : 0
1. : 1
2. : 10
3. : 11
4. : 100
5. : 101

etc... You can also fill up the left hand side with zeros, and they don't actually do anything, it's just easier to read. So based on your compiler when you create an int you are allocating X amount of bytes. 

When you look at the binary of an int in memory, you will see the 4 bytes (or 8 chars) as hexadecimal characters. Why do we use hexadecimal for this?? The problem is that base 10 doesn't line up with bytes very well. 16 is the maximum number that can be stored in half a byte which is 4 binary digits. 

## Bitshift left and right

These shift the bits to the left or the right by the amount of places specified.

```cpp
a = 5;
a <<= 1;
// this shifts 0101 to 1010
// the zero is inserted because there was nothing to the right
a == 10; // true
a >> 1;
a == 5; //true
// every time we shift we double the number
```

So a bitshift right or left is esentially just doubling or halfing the number x amount of times

### What happens if you bitshift odd numbers?

```cpp
5 >> 1;
// 0101 = 0010 = 2
// effectively you've halfed it and lost the decimal place
```

Basically it's integer division.

### How is this useful?

You can be an asshold and use them instead of multiplication operators.... but the compiler will be doing this anyway. So instead you should be cool and just use readable operators.

So you should still write code the way you used to...


Next video can be found [here](./BitwiseAndOrXorAndNotInCpp.md)



