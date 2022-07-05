# Depth testing

We've already made use of the **depth buffer** (or zbuffer) to prevent triangles rendering in front of each other. This is made possible because it stores **depth values** to determine where in zspace particles are. 

Just like the **color buffer** which stores all the fragment colors, the depth buffer sotores information per fragment (pixel). It's automatically created by the windowing systems and stores depth values as 16, 24, or 32 bit floats (usually 24). 

Depth testing is performed in screen spacer after the fragement shader has run. The coordinates relate directly to the viewport defined by OpenGl's glViewport function and can be accessed with gl_FragCoord variables in the fragement shader. The x and y represent the fagement's screen-space (0, 0 at bottom left corner), and the z represents the depth value of the fragment. 

!!! Note
    Modern Gpus have **early depth testing**. This allows the depth test to run before the fragment shader runs. This allows deth culling for fragments behind other fragments. The restriction of using early depth testing is that you can no longer write to the fragment's depth value. 

Depth testing is enabled like so:

```cpp
glEnable(GL_DEPTH_TEST);
```

Once this is enabled, fragments automatically store their z-values in the depth buffer, and are discarded if they fail accordingly. If you have depth testing enabled you should clear the buffer each frame:
```cpp
glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);
```

Sometimes you might want to perform the depth test on all fragments and discard them accordingly but *not* update the depth buffer. Basically useing a *read-only* depth buffer. You can disable writing to the depth buffer with 

```cpp
glDepthMask(GL_FALSE);
```

### Depth test function

```cpp
glDepthFunc(GL_LESS);
```

The above function works with the folowing operators:

| Function | Description | 
| -- | -- |
| GL_ALWAYS |  The depth test always passes | 
| GL_NEVER | The depth test never passes | 
| GL_LESS | (default) The depth test passes if the value is less than stored depth |  
| GL_EQUAL | The depth test passes if the value is equal than stored depth |  
| GL_LEQUAL | The depth test passes if the value is less than or equal than stored depth |  
| GL_GREATER| The depth test passes if the value is greater than stored depth |  
| GL_GEQAUL | The depth test passes if the value is greater than stored depth or equal |  
| GL_NOTGREATER| The depth test passes if the value not equal to the stored depth value|  

GL_ALWAYS is pretty much the same as not enabling GL_DEPTH_TEST at all. 

## Depth value precision

Depth values are stored in the depth buffer between 0.0 and 1.0. These map to the *near* and *far* plane of the projection matrix. They are transformed with a following linear equation:

$$
F_{depth} = \frac{
    z - {near}
}{
    {far} - {near
}
$$

In practice this **linear depth buffer** is almost never used. Because of projection properties a non-linear equation is used that is porportional to 1/z


$$
F_{depth} = \frac{
    1/z - 1/{near}
}{
    1/{far} - 1/{near}
}
$$

![depth_nonlinear_graph](./depth_non_linear_graph.png)

The nonlinearity of the depth buffer quickly become apparent when we visualize it. 

We can visualize the depth buffer by outputting it from the frag shader like so:

```GLSL
FragColor = vec4(vec3(gl_FragCoord.z), 1.0);
```

Running this will make everything look close to white. Because it is nonlinear, everything is nearly white. If you get really close to an object then you notice it getting a little darker. To transform the depth buffer back to linear space we can do the following in the shader:

```GLSL
#version 330 core
out vec4 FragColor;

float near = 0.1; 
float far  = 100.0; 
  
float LinearizeDepth(float depth) 
{
    float z = depth * 2.0 - 1.0; // back to NDC 
    return (2.0 * near * far) / (far + near - z * (far - near));	
}

void main()
{             
    float depth = LinearizeDepth(gl_FragCoord.z) / far; // divide by far for demonstration
    FragColor = vec4(vec3(depth), 1.0);
}
```

## Z-fighting

If two planes or triangles are so closely aligned that the depth buffer does not have enough precision to figure out which ones to draw on top. Really the best way to prevent this is to never place objects too close to each other. Another trick is to set the near plane as far as possible. This can add to the precision that is available. Otherwise you can use a hight precision depth buffer. 



