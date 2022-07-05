# CppCon 2017: John Lakos "Local ('Arena') Memory Allocators (part 1 of 2)"

<iframe width="560" height="315" src="https://www.youtube.com/embed/nZNd5FjSquk" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Memory allocators are not new. Video game programmers have known this for a long time, and it's absolutely essential for them to do their jobs.The only question is: Is it worth the effort? In C++ 20 and 2013 the effort of this will go to zero. 

## Important recurring Questions

### Are memory allocators really worth the trouble?

* What situations merit their use?
* How are they applied effectively?
* What's the performance impact?

Performance is one area where allocators make a big difference, but it's only one of these areas. We are talking about performance, but arguing the values of allocators there would be another 2 complete talks about why they are important. This talk is just performance. 

## 1. Introduction and Background

*What are memory allocators, and why are they useful?*

We should be proud that we are looking at the hardware a little bit. But we really just need to get the gist of it. We will talk about a general architecture, but benchmarks will be run on a specific architecture. 

#### Why do we like the C++ language?

* It enables us to "fine-tune" at a low level when needed.
* It can deliver very high runtime performance.

Otherwise we'd probably use something else.

#### Why (should) we care about memory allocators?

* They enable us to "find-tune" at a low level when needed
* They can help to improve runtime performance.

If we care about performance, we need to care about memory allocators. Not all memory is alike: there is fast, shared, protected, mapped... you can deal with this. You can do testing, debugging, measuring and profiling in a more controlled way. And of course it will enable enhanced runtime performance. Better locality, less contention. 

#### Common arguments against allocators

* Requires more up-front design effort. It's programming though, you should be good at this
* Complicates user interfaces.... depending on your design
* May actually degrade performance: if you choose the wrong allocator or there is none needed.

These can be addressed with well supported facts and careful measurements. 

### Review of Computer Memory

We have the CPU and then we have main Memory. Every time the cpu turns memory can get added. 

#### Cache Memory

A Cache sits between the CPU and Main Memory. This gives us a cache line with some memory in it. A cache line is a chunk of contiguous memory that gets pulled into the cache all at once. So the next time you want to access something on the cache line you already have it loaded. The cache can take something from anywhere in memory and put the line there. If you then add things to the same line in memory, it's instantly in the cache, since it was already loaded. Eventually we change something in the cache, and then we put that back into memory. There are many levels of cache but we don't need to care about that. Higher levels of cache include paging: Where you have x amount of pages in your real memory that you need to page in and out. There's many different levels of cache from registers on one hand to pages to virtual memory. 

What you need to know is that things that need to be quickly accessed a lot need to be close together in memory. This is what locality means. Not having locality is bad.

#### Main Memory Segments

We have the executable program which is loaded into memory along with all static variables, then we have stack memory growing from one direction and dynamic memory from the other. They grow through the use of **memory allocators**. It's just something that allows us to control memory. 

#### Memory Allocators

*A memory allocator organizes a region of computer memory, dispensing and reclaiming authorized access to suitable sub-regions on demand.*

* possibly non-contiguous

There are general purpose allocators and special-purpose allocators. General purpose allocators work for everybody and always do a good job. 

A Special purpose allocators works especially well for *some* use cases. They may not be save in a multi-threaded program, or they may not resuse individually freed memory. Using special purpose allocators requires specific knowledge of the context of use.

There are also global allocators and local allocators. A global allocator operators on a single ubiquitous region of memory, exists throughout the lifetime of a program and is inherently accessible from all parts of a program.

A local allocator operats on a local sub-region ("arena") of memory. It may exist for less than the lifetime of a program, and is (typcially) supplied for client use via a "reference". These can (typically) be used to free memory unilaterally.

In C we have
General-Purpose Global Allocator

```C
// malloc.h
void *malloc(size_t nBytes);
void free(void *address);
```

In C++ we have new

```C++
// <new>
namespace std{
    void *operator new(size_t nbytes);
    void operator delete(void *address);
}
```

This allows us to free and alloc from dynamic memory. They aren't allocators per-say they are syntax to work with them.

Special-Purpose Local Allocator

```C
// alloca.h
void *alloca(size_t nBytes);
```

This give you memory from the "hot" program stack. That's really good if you know what you're doing but if not your program will crash and burn.

|| **Global** | **Local** | 
| ------------- | ----------------- | ----------------- | 
|** General**| ```malloc/free```, ```new/delete```, tcmalloc, jemalloc | ```multipool_allocator```, Any general algorithm applied to a physically (and temporally) local region of memory | 
|**Special** | An unsynchronized *tmalloc* allocator "plugged into" ```malloc/free``` | ```alloca```, ```monotonic_allocator```, Unsynchronized version of a ```multipool_allocator``` |

If you can access something in quick succession and you can do it in a boxed region, local allocators are your friend. Otherwise, use global.  

*A memory allocator is a stateful **utility** or **mechanism** that organizes a region of computer memory, dispensing and reclaiming authorized access to suitable sub-regions on demand* 

* possibly non-contiguous

 The allocator itself has some logic in it, but local allocators are tied to a very specific region in memory.

 !!! No
    You can't copy an allocator. It's a region in memory and therefore singular. 

Every allocator you ever deal with is a pointer to some region in memory. The logic lives in the allocator object, but the memory lives in the region of memory it points to.

#### Local Allocator Mechanism

```cpp
class LocalAllocator{
    // internal data structure
public:
    // you can't copy an allocator 
    LocalAllocator(const LocalAllocator&) = delete;
    LocalAllocator& operator=(const LocalAllocator&) = delete;

    // CREATORS
    LocalAllocator(void *begin, void *end);

    // MANIPULATORS
    void *allocate(std::size_t nBytes);
    void deallocate(void *address);

    // in this real you have to know what you're doing
    void release(); // LOCAL ALLOCATORS ONLY 
}
```

*A memory allcoator is (the client-facing interface for) a **stateful** utility or mechanism that organizes a region of coputer memory, dispensing and reclaiming authorized access to suitable sub-regions on demand* 

* possibly non-contiguous

#### Memory Allocator Interfaces

Allocators can be supplied for use in mutliple ways:

1. As (stateful) utility functions.
    * Now I can freeee!
    * Doesn't support allocator objects

2. As a "reference wrapper" template parameter. 
    * passed in as a template parameter
    * Concrete allocator type is available for use by client's compiler
    * Forces a client to be a template in order to hold the allocator reference
    * Allocator type affects the C++ type of the client object
3. As the address of a pure abstract base class
    * Alocator can be held via a base-class reference by a non-template class
    * the choice of allocator does not affect the C++ type of the client object.
    * Allocator must be accessed via virtual-function interface
    * Object must somehow hold an extra address - even for the default case.

Have we measured this?

## 2. Understanding the Problem

*What aspects of software affect allocation strategy?*

Should we supply a local allocator? Which one? If No use Default Global Allocator.

If yes should it be done via the base class or bake it into the type C++ 11 style. This is a syntax issue. 

Which allocator should we use? 

Do we want to bother destroying each individual object and sub-object or just make them go away?

Use Optimal Local-Allocation Strategy

### Our Tool Chest of Allocation Strategies

#### **Global Allocator** 

We have two primary options for how it's implemented: 

1. Type Parameter
2. Abstract Base (Object that invokes new and delete)

2 Allocation Strategies

#### Local Allocators
To the block of memory created by the global allocator we now need to affix a pooling allocator. This can be

1. Monotonic
2. Multipool
3. Multipool<Monotonic>

Then Type Parameter or Abstract Base

Then Normal destruction or magically "Winked out". Winking out means it's just gone without saying anything

### 12 Allocation Strategies

| Label | Allocator Type | Allocator Binding | Desruction of Allocated Objects | 
| ----- | -------------- | ----------------- | ------------------------------- |
| AS1 | Default Global Allocator | Type Parameter | Normal Destruction |
| AS2 | New/Delete Allocator | Abstract Base | Normal Destruction |
| | | | |
| AS3 | Monotonic | Type Parameter | Normal Destruction |
| AS4 | Monotonic | Type Parameter | (magically) "Winked Out" |
| AS5 | Monotonic | Abstract Base | Normal Destruction |
| AS6 | Monotonic | Abstract Base | (magically) "Winked Out" |
| | | | |
| AS3 | Multipool | Type Parameter | Normal Destruction |
| AS4 | Multipool | Type Parameter | (magically) "Winked Out" |
| AS5 | Multipool | Abstract Base | Normal Destruction |
| AS6 | Multipool | Abstract Base | (magically) "Winked Out" |
| | | | |
| AS3 | Multipool<Monotonic> | Type Parameter | Normal Destruction |
| AS4 | Multipool<Monotonic> | Type Parameter | (magically) "Winked Out" |
| AS5 | Multipool<Monotonic> | Abstract Base | Normal Destruction |
| AS6 | Multipool<Monotonic> | Abstract Base | (magically) "Winked Out" |


#### AS1

```cpp
class allocator {
// no data members
public:
    // CREATORS
    allocator() {}
    allocator(const allocator&){}
    ~allocator() {}

    // MANIPULATORS
    allocator& operator=() = delete;
    void *allocate(std::size_t nBytes){
        return ::operator new(nBytes);
    }
    void deallocate(void *address){
        ::operator delete(address);
    }
};

FREE OPERATORS
bool operator==(const allocator&, const allocator&){
 return true;
}

myFunction(){
    std::vector<int> v;
}

myFunction(){
    // allocator is the TYPE PARAMETER for std::vector here. 
    // this is te same as myFunction above.
    std::vector<int, allocator> v;
}
```

Do a benchmark on the vector and destroy it like this:

```cpp
myBenchmark(){
    const int N = 1000;
    std::vector<std::list<int>*> system(N);

    for( int i{0}; i < N; ++i ){
        system[i] = new std::list<int>;
        // build up list of elements
    }
    // Do benchmark (e.g. access links)
    for (int i{0}; i< N){
        delete system[i];
    }

    // system goes out of scope
}
```

#### AS2

Same thing but with a pure abstract base class.

```cpp
class NewDeleteAllocator : public Allocator {
    // no data members
    public:
    // CREATORS
    NewDeleteAllocator() = default;
    ~NewDeleteAllocator() = default;
    NewDeleteAllocator( const NewDeleteAllocator& ) = delete;
    // MANIPULATORS
    void operator=(cost NewDeleteAllocator&) = delete;

    inline void *allocate(std::size_t nBytes) override {
        return ::operator new(nBytes);
    }
    inline void deallocate(void *address) override {
        ::operator delete(address);
    }
}
```

THESE ARE ACTUALLY INLINE AND HE IS NOT 





## 3. Analyzing the Benchmark Data

*When and how do you use which allocator, and why?*

## 4. Conclusions

*What must we remember about memory allocators?*







