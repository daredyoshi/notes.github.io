# Stencil testing

Once a fragement is processed, a **stencil test** is executed that has the option to discard fragments. This is based on the contents of the **stencil buffer**, which we can update during rendering to achieve cool effects. Each **stencil value** is an 8 bit value, and there are a total of 256 possible stencil valuse per pixel. We can set these values, and then filter for each one discarding other values. 

!!! Note 
    Each windowing library needs to set up the stencil buffer - this does not always happen automatically like with the depth buffer. GLFW does this automatically though. 

![stencil_buffer](./stencil_buffer.png)

The stencil buffer is first cleared which sets every fragment to 0. Then an open rectangle of 1s is stored, and only these fragements are rendered. The others are discareded. 

These stencil buffer operations allow us to set specific values while rendering fragements. This way we can discard certain fragments based on the fragments of other drawn objects in the scene. 

1. Enable writing to the stencil buffer. 
2. Render objects, updating the content
3. Disable writing to the stencil buffer
4. Render (other) objects, but only the parts that we want to as determined by the stencil buffer. 

To enable the stencil buffer you use GL_STENCIL_TEST

```cpp
glEnable(GL_STENCIL_TEST);
// don't forget to add the stencil buffer to the clear call
glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT | GL_STENCIL_BUFFER_BTI); 
```

To enable/disable writing you set custom bitmasks. Usually you will only need two

```cpp
glStencilMask(0xFF); // each bit is written to the stencil buffer as is
glStencilMask(0x00); // each bit ends up as 0 in the stencil buffer (disabling writes)
```

Similar to depth testing, there are several options about how to pass or fail the fragments for the stencil buffer. These are set with the following two functions

* **glStencilFunc**(GLenum func, GLint ref, GLuint mask)
* **func** set the test function of whether a frag passes or is disables. It has the same flags as mentioned in the [depth](./001_Depth_testing.md) chapter. 
* **ref** specifies the value a bit should hold for the test. 
* **mask** specifies the target bit

So for the example in the image at the top it would be

```cpp
glStencilFunc(GL_EQUAL, 1, 0xFF);
```

This only tell open gl if it should pass or discard frags based on the content, not how to actually update the buffer.

* **glStencilOp**(GLenum sfail, GLenum dpfail, GLenumdppass)
* **sfail** action to take if test fails
* **dpfail** action to take if test passes, but depth test fails
* **dppass** action to take if both pass

These are the possible action enums

| Action | Description | 
| -- | -- | 
| GL_KEEP | The currently stored stencil value is kept | 
| GL_ZERO | The stencil value is set to 0 | 
| GL_REPLACE | The stencil value is replaced with the value set with glStencilFunc | 
| GL_INCR | The stencil value is increased by 1 if it is lower than the maximum value of 256 | 
| GL_INCR_WRAP | The stencil value is increased but is reset to 0 if at the limit | 
| GL_DECR | The stencil value is decreased unless it's 0 | 
| GL_DECR_WRAP | The stencil value is decreased and if it is 0 it become 256 | 
| GL_INVERT | Bitwise inverts the current stencil buffer value | 

The defaults of glStencilOp are (GL_KEEP, GL_KEEP, GL_KEEP) so whatever the outcome, the stencil buffer is uneffected. If you want to write to the stencil buffer it needs to be an active decision. 

## Object outlining 

One common practical application for the stencil buffer is object outlining. The steps are as follows:

1. Enable stencil writing
2. Set the stencil op to GL_ALWAYS before drawing the objects to be outlined, updating the buffer with 1s wherever the object's fragments are rendered.
3. Render the objects. 
4. Disable stencil writing and depth testing
5. Scale each of the objects by a small amount. 
6. Use a different fragment shader that outputs a solid color
7. Draw the objects again, but only if the fragments' stencil values are not equal to 1
8. Enable depth testing again and restore stencil to GL_KEEP




