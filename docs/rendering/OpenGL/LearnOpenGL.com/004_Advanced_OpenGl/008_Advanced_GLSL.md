# Advanced GLSL

This chapter has *good to knows* and *features that may make your life easier*

## GLSL'S build-in variables

Because shaders are so pipelined, if you wan data from any source outside the current shader, that data needs to be passed through that pipeline. The built in variables makes it easier to gather and/or write data. This does not cover all the variables, so check the [wiki]("https://www.khronos.org/opengl/wiki/Built-in_Variable_(GLSL)") for all of them. 

### Vertex shader variables

**gl_Positions** is the clip-space output position vector of the vertex shader. Without is you can't render anything. 

**gl_PointSize** is a variables you can set when rendering geometry of type GL_POINTS where each vertex is rendered as a primitive. It sets the size of this primitive from the shader. You can also set it with glPointSize function, but that can't be influenced by the shader. You need to enable the shader exporting the point size with glEnable(GL_PROGRAM_POINT_SIZE). This is great for things like particle generation. 

**gl_VertexID** is a read-only shader variable that holds the current ID of the vertex we're drawing. If drawing with glDrawElements, this holds the curernt index, while if rendering with glDrawArrays it holds the current vertex. 

### Fragment shader variables

**gl_FragCoord** is the window or screen-space coordinate of the fragment. You could use this to calculate a different color value based on the screen coordinate of a fragment. 

**gl_FrontFacing** is a read-only boolean that returns if a fragment is front facing based on it's vertex winding order. You could use this to draw a different material on the insice of an object to the outside. 

**gl_FragDepth** is a read/write variable that you can use to set the depth of your fragment. This is helpful to control in which order objects are drawn on top of each other. It's major disadvantage is that setting this in the fragment shader then disables **early depth testing**. 

## Interface blocks

If you want to send large collections of data between shaders, you can define **interface blocks** that work a lot like structs. The only difference is that they are declared with the in/out keyword insteat of struct. 

```GLSL
out VS_OUT{
    vec2 TexCoords;
} vs out;

void main(){
    ...
    vs_out.TexCoords = aTexCoords;
}

------- frag --------

in VS_OUT{
    vec2 TexCoords;
} fs_in;

...
```

You just need to make sure that all of the parameters are the same, and that the types are named the same. 

## Uniform buffer objects

One annoying thing is setting the same variables on all the shaders of our rendering pipeline. **Uniform buffer objects** allow us to declare a set of *global* uniform variables that are the same for all shader programs. 

Because they are buffers, we create them with glGenBuffers as GL_UNIFORM_BUFFER. In the shader you access them like this:

```GLSL
layout (std140) uniform Matrices{
    mat4 projection;
    mat4 view;
};

// you can access these variables directly without a prefix
void main(){
    gl_Position = projection * view * model * vec4(aPos, 1.0);
}
```

But what does **layout std140** mean?? This states that the uniform block uses a specific layout called a **uniform block layout**. 

### Uniform block layout 

Because a uniform buffer is a buffer we need an attrib pointer to it. Consider the following buffer:

```GLSL
layout (std140) uniform ExampleBlock{
    float value;
    vec3 vector;
    mat4 matrix;
    float values[3];;
    bool boolean;
    int integer;
};
```

How do we know the size of this struct so that we can bind it to the GPU memory appropriately. GLSL uses a uniform memory layout called a **shaded** layout by default. Shared because it's shared between multiple programs. Getting the offsets in a shared layout is a lot of work though, so the general practice is to *not* use a shared layout but a **std140** layout. This standardizes all of the offsets. 

In a std140 layout, each variable has a **base alignment** equal to the space it takes up (including padding) within a uniform block. An **aligned offset (the byte offset of a varialbe from the start of the block) is calculated for each variable. This offset *must* be equal to a multiple of its base alignment. 

You can find the exact layout rules for each type [here](http://www.opengl.org/registry/specs/ARB/uniform_buffer_object.txt)

```GLSL
layout (std140) uniform ExampleBlock
{
                     // base alignment  // aligned offset
    float value;     // 4               // 0 
    vec3 vector;     // 16              // 16  (offset must be multiple of 16 so 4->16)
    mat4 matrix;     // 16              // 32  (column 0)
                     // 16              // 48  (column 1)
                     // 16              // 64  (column 2)
                     // 16              // 80  (column 3)
    float values[3]; // 16              // 96  (values[0])
                     // 16              // 112 (values[1])
                     // 16              // 128 (values[2])
    bool boolean;    // 4               // 144
    int integer;     // 4               // 148
};
```

It's not the most efficient layout, but it guarantees us that the layout remains the same in each program. There is also the *shared* layout, as mentioned above, as well as the *packed* layout. The packed layout optimizes for each program individually, and you have no guarantees as to the address of the variables. 

### Using uniform buffers

```cpp
unsigned int uboExampleBlock;
glGenBuffers(1, &uboExampleBlock);
glBindBuffer(GL_UNIFORM_BUFFER, uboExampleBlock);
glBufferData(GL_UNIFORM_BUFFER, 152, NULL, GL_STATIC_DRAW); // allocate 152 butes of memory
glBindBuffer(GL_UNIFORM_BUFFER, 0);
```

After this, whenever we want to update or insert data into the buffer, we bind it and then update it with glBufferSubData (as discussed in the previous chapter). The uniform will only need to get updated once, and all shaders will get it. 

When you bind multiple uniforms to programs, they attach at different binding points. By attaching the programs at the same binding points, they are sharing the same data in gpu memory.

![binding_points](./advanced_glsl_binding_points.png)

In the above you can see that both shaders are bount to the 0 binding point which is where the matrices are stored. To specify a specific binding point, you use glUniformBlockBinding and give it an index. This is how the shaders are able to shader the data. Note that you have to do this for every shader that uses the buffer

```GLSL
unsigned int lights_index = glGetUniformBlockIndex(shaderA.ID, "Lights");
glUniformBlockBinding(shaderA.ID, lights_index, 2);
```

!!! Note
    In OpenGL version 4.2, you can specify this in the shader itself effectively skipping the above step
    ```GLSL
    layout(std140, binding = 2) uniform Lights { ... };
    ```
    
After his we just need to bind the buffer object to that same binding point

```GLSL
// The function expects a target, a binding point index, and a uniform buffer object. 
glBindBufferBase(GL_UNIFORM_BUFFER, 2, uboExampleBlock);
// or 
// You can also just bind a specific range with 
glBindBufferRange(GL_UNIFORM_BUFFER, 2, uboExampleBlock, 0, 152);

// now we can start adding data. We can add all the data at once
// or just update parts of the buffer with glBufferSubData
glBindBuffer(GL_UNIFORM_BUFFER, uboExampleBlock);
int b = true; // update only the boolean value
glBufferSubData(GL_UNIFORM_BUFFER, 144, 4, &b);
glBindBuffer(GL_UNIFORM_BUFFER, 0);
```

Uniform buffer objects have severl advantages:

* Setting lots of uniforms at once is faster than setting multiple uniforms one at a time.
* Changing the same uniform over multiple shaders only need to be done once
* There is a much higher limit to the amount of uniforms in a buffer object than free uniforms that OpenGL can store. This come into play with skeletal animation for example. 





    







