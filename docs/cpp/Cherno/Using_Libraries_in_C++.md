# Using Libraries in C++
<iframe width="560" height="315" src="https://www.youtube.com/embed/or1dAmUO8k0" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


Ideally you should be able to just checkout a repository and compile and everything should just be able to run. People just want things to work. Because of this, usually we just copy code or binaries directly into the working directory of our solution. 

### Should we compile them ourselves? 

* Yes is it's a serious projet that you really want to work
* No if the libraries are platform specific
* No if it's just throwaway messing around

For windows there are often pre-build binaries. Static linking means the libraries is in the executable, a dynamic library get linked at runtime. Dynamic libraries are loaded at application launch. It's the difference of if the library gets loaded into your exe at compile time or runtime. Static linking is technically faster, but is less flexible. 

So we have #include files (header files) and ones that we link to at compile time.





