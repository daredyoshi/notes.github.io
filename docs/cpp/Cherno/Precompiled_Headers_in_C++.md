# Precompied Headers in C++

<iframe width="560" height="315" src="https://www.youtube.com/embed/eSI4wctZUto" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Procompiled headers aren't really required to be used, but they are vital for larger projects. They give you an opportunity to take a bunch of headerfiles and put them into a format where the compiler doesn't need to read them over and over again. 

## The Greatness of Pre-compiled headers
#### Example:

We use the std library a lot, strings, vectors etc. All these come from header files. Every time you include one of these, it needs to read the entire vector header file and compile it. This also goes for all the files that vector includes. Proabably about 1k lines of code. That whole mass of code has to be parsed and compiled every time. This isn't just for the main file - it's for every file that includes something. 

This means that compile times are a lot longer than they could be. Expcialy as you add more and more files. 

**Enter** pre-compiled headers. These parse each header file you want to include and then store it in a binary format that is much faster to parse. Every time we then include that header file, it already has everything you need. 

Basically this is many thousands of times faster especially as the projct size grows. 

## What not to do with pre-compiled headers

A pre-compiled header file is basically a heaer file which includes a bunch of other header files. The path of least resistance is therefore to put everything in your project into that PCH (pre-compiled header). Yes, that will speed up build times *BUT* **BUT** if you put stuff in there which changes as you are working.... everything will have to be re-built. And that could slow down your compilation. There's nothing wrong with putting your own stuff into there, but it shouldn't be stuff that changes a lot. It would make sense for something like a logging library, but not if you're currently working on it. 

What this is most useful for is external dependencies. STL or any head only stuff you are including. 

One Semantic thing to watch out for is that the PCH can potentially hide what is being used. For example if you use a windowing library in your PCH, you're not quite sure what parts a certain file needs. This can make it very difficult in terms of modularity when you want to move files between projects. 

Back to the example of the windowing library: if only one cpp file needs it, it's good practice to not put that file into your pre-compiled header. 

STL you definitly should be putting in there. 





