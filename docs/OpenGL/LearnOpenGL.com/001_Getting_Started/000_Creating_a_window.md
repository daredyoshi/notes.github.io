# OpenGL

### State Machine
By itself OpenGL is a large state machine: a collection of variables that define how OpenGL should currently operate. **State-changing** function change these variables which **stat-using** functions then query to see how they should execute. 

### Extensions
When a graphics company comes up with a new technique or optimizations for rendering, they create an **extension** in their drivers. If a developer's hardware supports this, they can then use these for more advanced graphics. Right now the hot one is raytracing. This way developers can use these features without having to wait for OpenGL to include the functionality in its future versions. If extensions are popular they usually get added to future OpenGL versions. The developer therefore needs to query if the extension is available

```cpp
if(GL_ARB_extension_name)
{
    // Do cool new and modern stuff supported by hardware
}
else
{
    // Extension not supported: do it the old way
}
```

### Objects
OpenGL libraries are written in C, and at its core remains a C-library. Because this doesn't always translate well to higher-level languages, there are several abstractions that got developed. One of these is objects. These objects get their settings set by the state machines. These objects are exposed as function that get and set the object members.

```cpp
// create object
unsigned int objectId = 0;
glGenObject(1, &objectId);
// bind/assign object to context
glBindObject(GL_WINDOW_TARGET, objectId);
// set options of object currently bound to GL_WINDOW_TARGET
glSetObjectOption(GL_WINDOW_TARGET, GL_OPTION_WINDOW_WIDTH,  800);
glSetObjectOption(GL_WINDOW_TARGET, GL_OPTION_WINDOW_HEIGHT, 600);
// set context target back to default
glBindObject(GL_WINDOW_TARGET, 0);
```

The above is a very common workflow. Create an object, store it's reference as an id ( the real data is store behind the scenes ). Then bind the object (using it's id) tot he target location of the context. Then you set the window options after the objects has been bound, and then unbind it. You are always binding objects to operate one, doing your thing, and then unbinding and binding other objects. 



