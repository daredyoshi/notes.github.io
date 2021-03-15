# Hello Triangle

OpenGL is all about 3d rendering, but the final output is a 2d array of pixels. A large part of the work is in transforming all 3d coordinates to 2d pixels that fit on your screen. This process is managed by the **graphics pipeline** of OpenGL. There are two parts to the graphics pipeline:

1. Transform your 3d coordinates into 2d coordinates 
2. Transform 2d coordinates into actual colored pixels 

GPUs today have thousands of small processing cores to process graphics data in parallel. When doing so, each of these cores is running a small program, called a **shader**. Some of these shaders are configurable by the developer, which allows us to write our own. They are written in the **OpenGL Shading Language (GLSL)**. Here's a cool image about the different steps:

![graphics_pipeline](./pipeline.png)

Each section of the graphics pipeline handles one specific part of converting our vertex data. At the beginning of the pipeline we pass in a list of 3d coordinates that should form a triangle. A **vertex** is a collection of data per 3d coordinate. This data is represented using **vertex attributes** that contain any data we'd like. One common data type is a color vector. 

!!! note
    OpenGL doesn't know what our input data is without hints called **primitives**. These are given to OpenGL while calling any of the drawing commands. Examples are GL_POINTS, GL_TRIANGLES and GL_LINE_STRIP

Going over the pipeline in each step:

1. The **vertex shader** takes as input a single vertex. This allows us to do pre-processing on the vertex attributes. 
2. The **primitive assemply** aka **shape assembly** takes all the vertices (or vertex if a GL_POINTS primitive) that form a primitive and assembles these points in the primitive shape given.
3. The **geometry shader** gets an input collection of vertices that form a primitive and can change it by emitting new vetices to form new (or other) primitives. In this case it subdivides the triangle
4. This is then passed to the **rasterization stage** where the resulting primitives are mapped to pixels on the final screen. **Clipping** is performed during this stage, which discards all fragments that are outside your view.
5. These pixels are passed to the **fragment shader**, where colors are assigned to the pixels. This is where most of OpenGl's advanced effects occur. 
6. The final object then passes through an **alpha test** and **blending** stage, where it's sorted in z space before and behind other objects. This checks the opacity of the object, and blends objects accordingly. 

We are **required** to define at least a vertex and fragment shader of our own. 


### Vertex input

All vertices are given in 3d (xyz) coordinates. Their coordinates are always between -1.0 and 1.0 on all 3 axes. This is what's called **normalized device coordinates** and will then be converted to "screen space" - not your virtual world's space. The step to convert your 3d geometry into NDC space will be covered in a later chapter. When converting your vertices into **screen-space coordinates** you will use the **viewport transform** using the data you provided with glViewport.

We would like to send the following triangle (in NDC space) through our pipeline:

```cpp
float vertices[] = {
    -0.5f, -0.5f, 0.0f,
     0.5f, -0.5f, 0.0f,
     0.0f,  0.5f, 0.0f
};
```

First it needs to go to the vertex shader. To send this data there we need to create memory on the GPU to store it, and configure how OpenGL should interpret this memory. We manage this memory via **vertex buffer objects (VBO)** that can store a ton of vertices in GPU memory. This allows us to dump large batches of data onto the GPU in a single operation. Here we encounter our first OpenGL object:

```cpp
// create our vertex buffer object. This will pass the data to the gpu
// all objects in openGL are access via an id. 
unsigned int VBO;
// create vertex buffers for this id
glGenBuffers(1, &VBO);
// to edit this object we need to bind the id to openGL
// you can bind one object of each type at a time before having to unbind
glBindBuffer(GL_ARRAY_BUFFER, VBO);
// now any buffer calls we make (on the GL_ARRAY_BUFFER target) will
// be used to configure the currently bound buffer
// now bind our vertices to the array buffer
// This function allocates memory and stores the data within the 
// curerntly bound buffer object
// There are three ways that this function can treat the data
//      GL_STREAM_DRAW: the data is set only once and used by the GPU at most a few times
//      GL_STATIC_DRAW: the data is set only once and used many times
//      GL_DYNAMIC_DRAW: the data is changed a lot and used manytimes
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
```

### Vertex Shader

This is one of the requierd shaders if we want to set up some rendering. To use it we need to write a shader in GLSL, the OpenGL Shading Language, and then compille this shader so we can use it in our application. 

```GLSL
// the syntax is very similar to C
// usually the input data for a shader is not already in NDC, so
// the shader will need to do a bit more work than it's doing here.
// Our shader is verrrry simple though, and is just forwarding the position
// of our vertices which we are conveniently giving in NDC.

# version 330 core

// declare all the input vertex attributes with the in keyword.
// the default vector datatype has 4 floats, so we are using a vec3
// for the position. 
layout (location = 0) in vec3 aPos;

// to set the output of the vertex shader we need to assign a vector4 value
// to each vertex to a predefined variable called gl_Position. 
// gl_Position will be used as the output of the vertex shader. 
void main(){
    gl_Position = vec4(aPos.x, aPos.y, aPos.z, 1.0);
}
```

!!! note
    Vectors in OpenFL have 4 floats. The fourth number is used for **perspective division**. This will be discussed more later

### Fragment shader

This is the other required shader for rendering a triangle. Colors are once again an array of 4 values, but the fourth value has nothing to do with perspective division, but alpha. The color values range from 0.0 to 1.0.

```cpp
#version 330 core
// The fragment shader only requires one output variable similar
// to the gl_Position for the vertx shader. In this case it's named
// more nicely as FragColor.
out vec4 FragColor;

void main(){
    FragColor = vec4(1.0f, 0.5f, 0.2f, 1.0f);
}
```

### Shader program

The final linked version of multiple shader combined is called a **shader program object**. To use the shaders we just created we need to **link** them to a shader ptogram object, and then activate this shader when rendering objects.

```cpp
// To use the compiled shader we have to link them to a shader program object
// The ative shader program's shader will be used when we issue render calls.
// This is also where you'll get linking errors if your outputs and inputs don't match
unsigned int shaderProgram{glCreateProgram()};

// not attach the compiled shaders to the program
glAttachShader(shaderProgram, vertexShader);
glAttachShader(shaderProgram, fragmentShader);
// This links all the attached shaders in one final shader program object
glLinkProgram(shaderProgram);

// now let's test if the linking was successful
glGetProgramiv(shaderProgram, GL_LINK_STATUS, &success);
if(!success){
    glGetProgramInfoLog(shaderProgram, 512, NULL, infoLog);
    std::cout << "ERROR::SHADERPROGRAM::FRAG::COMPILATIONS_FAILED\n" << infoLog << '\n
}

// We can now activate the program object
// From now one every shader and rendering call will use this program object
glUseProgram(shaderProgram);

// don't forget to delete the shader objects now that we've linked them
glDeleteShader(vertexShader);
glDeleteShader(fragmentShader);
```

### Linking Vertex Attributes

Now we sent the input vertex data to the GPU and instructed the GPU how it should process the data in the shaders. OpenGL still doesn't know how it should connect the vertex data to the vertex shaders' attributes. We need to specify how OpenGL should interpret the vertex data before rendering. 

Our vertex buffer data is formatted as follows:
[!vertex_buffer](./vertex_attribute_pointer.png)

* The position data is stored as a 32-bit (4 byte) floating point values
* Each position is composed of 3 of those values
* There is no space (or other values) between each set of 3 values. They are **tightly packed** in the array
* The first value in the data is at the beginning of the buffer.

```cpp
// We will now tell OpenGL how it should interpret the vertex data (per attribute)
//
//
// The first param specifies which vertex attribute we want to configure. (location = 0)
// The next argument specifies the size of the attribute
// The next is the type of the attribute
// The next is if we want to force normalize the data.
// The next is how large our stride is. This is the space between consecutive vertex a
// (we know that the array is githly packed so this could also have been 0)
// The last parameter sets the offset of where the data begins in the buffer
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
// 0 is the intex of the vertex attrib array we want to activate
glEnableVertexAttribArray(0);
```

The above process needs to repeat for every single object we are drawing, so it would be useful to be able to save these configurations. 

### Vertext Array Object

A **vertex array object (VAO)** can be found just like a vertex buffer object. Any subsequent vertex attribute calls will then be stored in the VAO. This way you only need to make those calls once when configuring vertex attribute pointers. Whenever we want to draw the object, we can just bind the corresponding VAO. This way we can switch between different vertex data and attribute configurations as easily as binding different VAOs. Everything we set above is stored inside the VAO.

!!! note
    Core OpenGL *requires* that we use a VAO so it knows what to do with our vertex intputs. It will refuse to draw anything without a VAO.
    

```cpp
// ..:: Initialization code (done once (unless your object frequently changes)) :: ..
// 1. bind Vertex Array Object
glBindVertexArray(VAO);
// 2. copy our vertices array in a buffer for OpenGL to use
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
// 3. then set our vertex attributes pointers
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);  

  
[...]

// ..:: Drawing code (in render loop) :: ..
// 4. draw the object
glUseProgram(shaderProgram);
glBindVertexArray(VAO);
someOpenGLFunctionThatDrawsOurTriangle();   
```

### Element Buffer Objects

What if we want to render a rectangle? Should we input twice the amount of vertices? That would be totally inefficient!

```cpp
float vertices[] = {
    // first triangle
     0.5f,  0.5f, 0.0f,  // top right
     0.5f, -0.5f, 0.0f,  // bottom right
    -0.5f,  0.5f, 0.0f,  // top left 
    // second triangle
     0.5f, -0.5f, 0.0f,  // bottom right
    -0.5f, -0.5f, 0.0f,  // bottom left
    -0.5f,  0.5f, 0.0f   // top left
}; 
```

This problem will only get worse with more complex objects. It would be better to store only the unique locations, and then indices in that array to determine how the triangles get made. **EBO (element buffer objects)** are additional object buffers that store indices that OpenGL uses to decide how to make triangles out of the vertices. This process is known as **indexed drawing**. 

```
float vertices[] = {
     0.5f,  0.5f, 0.0f,  // top right
     0.5f, -0.5f, 0.0f,  // bottom right
    -0.5f, -0.5f, 0.0f,  // bottom left
    -0.5f,  0.5f, 0.0f   // top left
};
unsigned int indices[] = {  // note that we start from 0!
    0, 1, 3,   // first triangle
    1, 2, 3    // second triangle
};
```

It's created and bound just like the VBO

```cpp
// ..:: Initialization code :: ..
// 1. bind Vertex Array Object
glBindVertexArray(VAO);
// 2. copy our vertices array in a vertex buffer for OpenGL to use
glBindBuffer(GL_ARRAY_BUFFER, VBO);
glBufferData(GL_ARRAY_BUFFER, sizeof(vertices), vertices, GL_STATIC_DRAW);
// 3. copy our index array in a element buffer for OpenGL to use
glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, EBO);
glBufferData(GL_ELEMENT_ARRAY_BUFFER, sizeof(indices), indices, GL_STATIC_DRAW);
// 4. then set the vertex attributes pointers
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)0);
glEnableVertexAttribArray(0);  

[...]
  
// ..:: Drawing code (in render loop) :: ..
glUseProgram(shaderProgram);
glBindVertexArray(VAO);
glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0)
glBindVertexArray(0);
```




