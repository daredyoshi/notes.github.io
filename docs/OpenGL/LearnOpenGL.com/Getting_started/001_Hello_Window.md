# Hello Window


!!! note
    Make sure to include glad BEFORE GLFW!!

We're now going to setup a basic window as described [here](https://www.glfw.org/docs/latest/window.html#window_hints)

Applications drawing to a buffer required a **double buffer** in order to avoid flickering issues. Because not all the pixels are rendered at once, the double buffer method waits for the second buffer to finish drawing before replacing the pixels. Otherwise it may contain artifacts. The **front** buffer contains the final output image that is shown at the screen, while all the rendering commands draw to the **back** buffer. When they are finished, we then **swap** the back buffer to the front buffer so the image can be displayed without still being rendered to. 

GLFW has callback functions that you can bind to. You pass functions into these by reference, and glfw calls them when the appropriate callback activates. 

```cpp
void framebuffer_size_callback(GLFWwindow* window, int width, int height)
{
    glViewport(0, 0, width, height);
}

glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);
```

A frame is a single iteration of the render loop. Basically every time the buffers have been switched. The render loop looks like this:

```cpp
while (!glfwWindowShouldClose(window))
{
    processInput(window);

    glfwSwapBuffers(window);
    glfwPollEvents();
}
```

