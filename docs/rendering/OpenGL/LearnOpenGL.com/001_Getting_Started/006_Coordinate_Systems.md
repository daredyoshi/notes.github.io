# Coordinate Systems

OpenGL expects all vertices that we want to become visible to be in NDC (normalized device coordinates) after each vertex shader run. X, y and z should all be between -1.0 and 1.0. 

This transformation usually happens in several steps. Because some operations are easier to do in different *intermediate* coordinate systems, we don't just transform everything directly into NDC. These are

![coordinate_systems](../coordinate_systems.png)

### Local space

This is where the vertices of the object start out in. All the vertices are in local space. All the vertices are relative to the origin of the object. 

### World Space

Where are the objects in the scene composition? How do they relate to each other? The world coordinates are the vertices of your object multiplied with the **model** matrix. This is the transformation matrix that puts your object where it belongs. 

## View Space

The orthographic camera *direction*. This is the result of transforming all the vertices by the **view matrix**. In open GL you don't move the camera, you move the world. 

## Clip Space

All coordinates that do not fall into this space -1.0 to 1.0 get clipped. Before this happens, we create our own coordinate system. Vertex coordinates get transformed from view to clip-space with a **projection matrix** that goes from a range in each dimension. This then converts all coordinates into NDC. 

!!! Note
    OpenGL will reconstruct clipped triangles with one or more triangles that fit inside the clipping range

The *viewing box* that the projection matrix creates is called a **frustrum**, and all the coordinates inside this frustrum will end up on the user's screen. The entire process to convert coordinates within a range to NDC is called **projection**. 

Once all the vertices are transformed to to clip space, **perspective division** is applied to them where we divide x, y and z by the w. After this the coordinate are mapped to the screen and become fragments. 

There are two types of projection matrices to use

* Orthographic projection

Like a blueprint, this has an infinitely small angle. This **directly** maps all the coordinates inside the target range to the frustrum without any perspective. You can do this by setting the w coordinate of all vertices to 1.0

To create this projection matrix we can use glm::ortho

```cpp
// the first two parameters specify the left and right
// the second two up and down 
// the last two the distances between near and far
glm::ortho(0.0f, 800.0f, 0.0f, 600.0f, 0.1f, 100.0f);
```

* Perspective projection

In *real life* objects get smaller the farther away from you the get. This is called **perspective**. The projection matrix mimix this with a **perspective projection matrix**. This maps a given frustrum range to clip space, but also manipulates the w value of each vertex coordinaet in such a way that verticse farther away go closer to 0 (the vanishing point). 

To create this you can use glm::mat4

```cpp
// The first parameter defines the field of view
// the second parameter sets the aspect ratio
// the third and fourth set the near and far clip plane. 
glm::mat4 proj = glm::perspective(glm::radians(45.0f), (float)width/(float)height, 0.1f, 100.0f);
```

## Putting it all together


$$
V_{ clip  }= M_{ projection  }* M_{ view  }* M_{ model  }* V_{local}
$$

Moving the camera backwards is the same as moving the entire scene forwards. 


If you don't want zfighting, you need to enable the zbuffer.


