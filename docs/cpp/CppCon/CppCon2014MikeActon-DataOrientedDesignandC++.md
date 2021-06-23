# CppCon 2014: Mike Acton "Data-Oriented Design and C++"

<iframe width="560" height="315" src="https://www.youtube.com/embed/rX0ItVEVjHc" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


## What's important to game devs?

* Hard deadlines
* Soft realtime performance requirements (soft = 32 ms)
* Usability
* Performance
* Maintenance
* Debugability

## What languages do we use..?

* C
* C++ <- about 70%
* Asm <- most preferred
* Perl
* Javascript
* C#
* Pixel shaders, vertex shader, geometry shaders etc.

## How are games like the Mars rovers

* Exceptions (avoid at all costs)
* Templates (too long compile times)
* Iostream (not really used)
* Multiple inheriance (just dumb)
* Operator overloading (maybe if it's super obvious but don't be silly)
* RTTI (off the table)
* STL (it's not that we have a replacement for this it just don't solve the problems that we want to solve)
* Custom allocators ( we allocate all memory up front and divide things into their own heirarchies )
* Custom Debugging Tools ... we have tools for everything

## Is data-oriented even a thing...?



!!! Philosophy
    The purpose of all programs, and all parts of those programs, is to transform data from one form to another. 

!!! Philosophy
    If you don't understand the data you don't understand the problem at all. 
    You understand a problem better by understanding the data better

!!! Philosophy 
    Different problems require different solutions.

If you have different data, you have different problem. I fyou don't understand the cost of sovling the problem, you don't understand the problem. If you don't understnad the hardware, you can't reason about the cost of solving the problem. Everything is a data problem. Including usability, maintenance, debug-ability, etc. Everything. Solving problems you probably don't have creates more problems you definitly do.

Latency and throughput are only the same in sequential systems. 

!!! Rule of thumb
    Where there is one, there are many. Try looking on the time axis. 


!!! Rule of thumb
    The more context you have, the better you can make the solutino. Don't thow away data you need. 


!!! Rule of thumb
    NUMA extends to i?O and pr-built data all the way back through time to original source creation. 

Software does not run in a magic fairy aether powered by the fevered dreams of CS PhDs. Ultimately reason must prevail. This is all what data-oriented means. This is a response to the broader culture of C++, and...

## The three big lies:

### 1. Software is a Platform

**Hardware is the platform** - different hardware, different solutions. Different physical constraints apply. Reality is not a hack you're forced to deal with to sove your theoretical problem. Reality is the actual problem.   

### 2. Code should be designed around model of the world

This has trickled into cpp culture from Oop. Hiding data is implicit in this world modeling - even the point of it. It confuses the maintenance, and the properties of the data. The cost of slightly better maintinence can make it much more difficult to solve problems really well. In real life classes of things are actually the things, but in terms of data transformations, "classes" are really only superficially similar. How similar is a game static chair to a physical chair? There is nothing the same, but because they have a world modeling similarity they should be connected which is nonsensical. This leads to monolithic, unrelated data structures. It tries to idealize the problem away. But you can't make a problem simpler than it is. The reality is you have this actual set of hardware. World modeling is kind of like self-help books for programming, solving by analogy and storytelling instead of by hardware.  

### 3. Code is more important than data. 

So much development time is spent talking about the code, while most of the time the code is a minor issue. The data we are dealing with and what we are doing with it is the actual problem. The **only purpose of any code is to transform data**. The programmer is fundamentally responsible for the data, not the code. The code is just the tool used for the transformations. Your job is to solve the transformation problems. Only write code that has direct provable value (i.e. transforms data in meaningful way)

Understand the data to understand the ptorlbem. 

Tehre is no ideal, abstract solution to the problem. You can't "Future proof" you code against future data because you do not know future data.  

## What problems do these lies cause?

Poor performance, concurrency, optimizability, stability, testability.

Solve for transfoming the data you have given the constraints of the platform (and nothing else). E.g. Dictionary lookup. You would expect key-value pair to be close to each other in memory. The reality is that they are not next to each other. The statistical chance that we will need value on any key is very low. Usually we will be iterating over the keys. That's what the data tells us that's what the problem is. This leads to doing unnecessary things because of a mental model. 

!!! Principle 
    Solve for the most common case first, not the most generic.

## Review of Ram Speeds

* L1 blazing fast basically instant
* L2 20 cpu cycles
* Main Ram 200+ cpu cycles 

The most significant part of our time will be spent waiting for L2 cache/misses. The incredibly slow process from loading something from main ram. Not even including shared memory modes between CPU and GPU and the different platforms associated with that. 

## How to Data Orient
A very simple example

```cpp
class GameObject {
    float m_pos[2];
    float m_velocity[2];
    char m_name[32];
    model* m_model;
    // etc
    float m_foo;

    void UpdateFoo(float f){
        float mag = sqrtf(
        m_velocity[0] * m_velocity[0] +
        m_velocity[1] * m_velocity[1]
                );
        m_foo += mag * f;
    }
};

```

We think looking at it that this sqrtf is the problem, but we know it's an order of magnitude less than access to ram. Breaking it down the majority of the work is in the initial read and the final read with the moves. The actual time is in the initial read and the final read-add. It's 10:1 for time spent during read : time spent with squrf. The vast majority of problems are ones the compiler can't reason about. **Compiler is a tool, not a magic wand**. It cannot solve the most significant problems that you have. 

So how do we use our capacity a little bit better? The best way is to make sure to fill that line with data we actually need. If we have multiple we might as well transform them together. Then we might as well pack them into the same cache line. By processing *count* of Foo we can be much more efficient per foo. 

```cpp
// 12 bytes x count(32) = 384 = 64 x 6
struct FooUpdateIn {
    float m_Velocity[2];
    float m_Foo;
};

// 4 bytes x count(32) = 128 = 64 x 2
struct FooUpdateOut {
    float m_Foo;
};

void updateFoos( const FooUpdateIn* in, size_t count, FooUpdateOut* out, float f ){
    for (size_t i = 0; i<count; ++i){
        float mag = sqrtf(
            in[i].m_velocity[0] * in[i].m_velocity[0] +
            in[i].m_velocity[1] * in[i].m_velocity[1]); 
            // (6/32) = ~5.33 loop/cache line
            // Sqrt + math = ~40 x 5.33 = 213.33 cycles/cache line 

        out[i].m_Foo = in[i].m_Foo + mag * f;
    }
}
```

Using the whole capacity of the cache line is approximately a 10x speedup. Just using the whole line at all already makes a massive difference when iterating over this many operations. The previous example is much harder to reason about what to change. Here it's much easier. 

In additions to this:

1. Code is maintainable
2. Code is debugable
3. Can REASON about cost of change

Ignoring inconvenient facts is not engineering; It's dogma. We have to take the reality of what we're doing into account - it's only professional. 

### Bools in Structs

Let's reason a little bit about the cost of doing this. Let's assume the bools are at least packed. We can at least say it's got a very low information density because we are wasting a bool on a byte. We are pushin other objects out of cache lines because of that one extra bit needed for a bool. They are also common last minute problems in decision making.  

We can use more of this line. There are several approaches to this:

A. Make same decision x512
B. Combine with other reads/xforms
    This is generally the simplest

* But things cannot exist in an abstract bubble - we need to know what other things are going on at the same time in order to merge them. 
* The will require context

C. Over-frames.. i.e. Only read when needed. 

If you process instructions over time, we can calculate when it will spawn and then push it into that instruction stream in the future. Not incrementing it every frame but calculating when it will pass the threshold once and then queing it for that.

### Ogre Engine example

Lots of bools and last min decision making example. The node class is designed around a "one at a time" approach. In the most common places nodes are in a hierarchy and they will get looped over. The genericness of separating the node costs a lot because you are separating all of the data from that node as well.

Complex constructors tend to imply that:

* Reads are unmanaged (one at a time...)
* Unnecessary reads/writes in destructors
* Unmanaged icache (i.e. virtuals) -> unmanaged reads/writes

We have no idea what is getting loaded for all data because we don't know what's going on in the constructor which makes everything slower. We can also see unnecessarily complicated state machines. 

!!! RuleOvThumb
    Store each state type separately. Store same states together. (No state value needed)

Another thing to avoid is initializing default variables when they will most likely be overwritten somewhere else later. Zero initialization is important to prevent unnecessary reads and writes. This problem is compounded even more when you don't know exactly what will happen with a class. 

!!! Fact 
    Strings are generally BAD - especially filenames

!!! RuleOvThumb
    The best code is code that doesn't need to exists. Do it offline. Do it once. 
    e.g. precompiled string hashes. 

We pay a heavy price for the mental model that a "node" exists alone in the world. This prevents us from aligning it's data more efficiently in memory and chaining operations together more efficiently. Separate the code into small functions instead of a switch statement. For example make 3 functions to translate a collection of nodes in local, world and parent space instead of using member functions to do the same thing. 

Then you make distinctions between use-cases like in-game vs. editor. They are two problems and they should be solved separately. Then reduce based on the most statistically common case. 

Organizing based on your data makes maintenance, debugging and concurrency much easier. The problem is that it's harder to program this way. 

## #Truths

1. Hardware is the Platform
2. Design around the data, not an idealized world
3. Your main responsibility is to transform data, solve that first, not the code design

You main responsibility as a programmer is to transform data. Bad news is that there's a culture of hiding things. 

!!! Quote
    Design patterns are spoonfeed material for brainless programmers incapable of independant thought, who will be resolved to producing code as mediocre as the design patterns they use to create it. 

The end














