# Advanced Data

There are some alternative approaches to managing buffer. Essentially a buffer is just an object that manages a certain portion of GPU memory. We give meaning to the buffer by defining a specific **buffer target**. We can also bind NULL as the argument for a buffer, if we want to *reserve* a specific amount of memory and come back to it later. 

We can also fill specific regions of a buffer with **glBufferSubData**. This allows us to only updace certain regions of the buffer's memory. Note that you need enough memory allocated for this to fill.

```cpp
glBufferSubData(GL_ARRAY_BUFFER, 24, sizeof(data), &data);
```

You can also get a pointer to the buffer's memory directly and copy the data in yourself. 

```cpp
float data[] = ...
glBindBuffer(GL_ARRAY_BUFFER, buffer);
// get pointer
void *ptr = glMapBuffer(GL_ARRAY_BUFFER, GL_WRITE_ONLY);
// copy the data into memory
memcpy(ptr, data, sizeof(data));
// tell opengl that we are done with the buffer
glUnmapBuffer(GL_ARRAY_BUFFER);
```

This way we can directly map data to a buffer, without first allocating temporary memory. 

## Batching vertex attributes

We can specify the attribute layout of the vertex array buffer's content with **glVertexAttribPointer**. Instead of interleaving the data, 123123123123 we can also use a batched approach 111222333. The batching approach makes individual elements of data easier to update with **glBufferSubData**. 

```cpp
float positions[] { ... };
float normals[] { ...  };
float uvs[] { ... };
// fill the buffer
glBufferSubData(GL_ARRAY_BUFFER, 0, sizeof(positions), &positions);
glBufferSubData(GL_ARRAY_BUFFER, sizeof(positions), sizeof(normals), &normals);
glBufferSubData(GL_ARRAY_BUFFER, sizeof(positions) + sizeof(normals), sizeof(uvs), &uvs);
// update the vertex attrib pointers to reflect thsoe changes
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), 0);
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 3 * sizeof(float), (void*)(sizeof(positions)));
glVertexAttribPointer(2, 2, GL_FLOAT, GL_FALSE, 2 * sizeof(float), (void*)sizeof(positions) + sizeof(normals)));
```

Now the stride parameter is the same for all the vertx attributes, and only the offset is really different. This shouldn't be used if you are not updating the buffer though, the interleaved approach is faster for the GPU to read. 

## Copying buffers

You can copy data from one buffer into another one directly. To do this you need to define a from and to target. This could be VERTEX_ARRAY_BUFFER and VERTEX_ELEMENT_ARRAY_BUFFER, but if you want to copy from and to vertex buffers you can use GL_COPY_READ_BUFFER and GL_COPY_WRITE_BUFFER. 

```cpp
// this 
glBindBuffer(GL_COPY_READ_BUFFER, vbo1);
glBindBuffer(GL_COPY_WRITE_BUFFER, vbo2);
glCopyBufferSubData(GL_COPY_READ_BUFFER, GL_COPY_WRITE_BUFFER, 0, 0, 8 * sizeof(float));
// could also be done like this
glBindBuffer(GL_ARRAY_BUFFER, vbo1);
glBindBuffer(GL_COPY_WRITE_BUFFER, 0, 0, 8 * sizeof(float));
```





