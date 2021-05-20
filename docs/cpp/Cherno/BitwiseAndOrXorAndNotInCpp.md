# Bitwise AND (&), OR(|), XOR(^) and NOT (~) in C++

<iframe width="560" height="315" src="https://www.youtube.com/embed/HoQhw6_1NAA" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

This continues from [ here ]( ./IntroToBinaryAndBitwiseOperatorsInCpp.md )

## AND ( & ) and OR ( | ) and XOR ( ^ )

These all operate on two inputs.

### AND (&)

& works very similarly to logical AND (&&), but they are very different things. & is a bitwise operator.

```cpp
0 & 0 = 0;
0 & 1 = 0;
1 & 0 = 0;
1 & 1 = 1;
```

```
    0101
&   1101
--------
    0101 // this is how it works on bits
```

Both sides need to be true for the result to be true. 

Very commonly this & is used as a bitwise mask. What the hell does that mean? If we have some random number in buts, we can use & to find the value of an index in a bit number

```
// say we want the value of the following bit
       ↓
    10110010101
    00010000000
   &___________
    00010000000

// if it had been 0 the result would also have been zero. 
// now you can use that to test if a certain index is zero or not

if(result) // result just needs to not be zero ( in this case it's 32 )

// You can also clear a bunch of bits this way

    101100101
    000001111
   &_________
    000000101
```

### OR (|)

If either of them are true, the result is true. 

```cpp
0 | 0 = 0;
0 | 1 = 1;
1 | 0 = 1;
1 | 1 = 1;
```

Just like Add can mask bits out, you can add bits back with OR. 

```
    000000101
    101100000 // these have been bitshifted to the left by 4
   |_________
    101100101 // back to the original value
```

In Actual code

```cpp
int a = 0b101;
int b = 0b10110
b <<= 4;
int c = a | b; // c = 10110101
```

### NOT (~)

This is not used very opften, but it's very useful for inverting a certain bit. 

```
// first you islate the bit you want say the 6th one and then not that
~000100000
// becomes
 111011111
// then  you & that to a byte
&101100101
// becomes
 101000101
```

### XOR (^)

This has mostly been used in hashing. This gives a 1 only if they are different

```cpp
0 ^ 0 = 0;
0 ^ 1 = 1;
1 ^ 0 = 1;
1 ^ 1 = 0;
```

If you XOR a value with itself it will always give you zero

```cpp
int a = 1101;
int b = a ^ a; // =0
```

This is really useful when checking for pairs in a vector

```cpp
int[] A = { 9, 3, 9, 3, 9, 7, 9};
int result = 0;
foreach (var i in A)
    result ^= i;
Console.WriteLine(result); // 7
```


