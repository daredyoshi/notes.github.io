# Framebuffers

So far we've used several types of screen buffers: color, depth, and stencil. The combination of these buffers is stored in GPU memory in a **framebuffer**. In OpenGL we can define our own framebuffers. 

```cpp
unsigned int FBO;
glGenFramebuffers(1, &FBO);
glBindFramebuffer(GL_FRAMEBUFFER, FBO);
glDeleteFramebuffers(1, &FB0);
```

By binding to GL_FRAMEBUFFER all upcoming read and write framebuffer operations will be performed on the newly bound framebuffer. You can also just bind a read/write framebuffer with GL_READ_FRAMEBUFFER or GL_DRAW_FRAMEBUFFER respectively. The read framebuffer will be used for all read operations like glReadPixels and all rendering is done to the draw framebuffer. We cannot yet use the above framebuffer because it is not yet complete. The following list is the requirements for a complete framebuffer. 

* We have to attach at least one buffer
* there should be at least one color attachment
* All attachments should be complete with memory reserved
* Each buffer should have the same number of samples

To check if the framebuffer is complete:

```cpp
if(glCheckFramebufferStatus(GL_FRAMEBUFFER) == GL_FRAMEBUFFER_COMPLETE)
// execute victory dance
```

So we need to create some sort of attachments to the buffer. 

## Texture attachments

If you attach a texture to a framebuffer, all rendering commands will write out to the texture as if it was a normal color/depth/stencil buffer. This way we can then easily re-use that color information in our shaders. 

```
// create a texture for the framebuffer to write to
unsigned int tex;
glGenTextures(1, &tex);
glBindTexture(GL_TEXTURE_2D, tex);

// allocate memory for the texture to be written to 
// the main difference to a regular texture is that we assign null values
// we also don't care about wrapping or mipmapping methods in this case
glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, 800, 600, 0, GL_RGB, GL_UNSIGNED_BYTE, NULL);

glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);

// attach the texture to the framebuffer
// args:
// target: the target we are writing to (draw, read or both)
// attachemtn: the type of attachment
// textarget: the type of textuer to attach
// texture: the texture to attach
// level: the level of mipmapping
glFramebufferTexture2D(GL_FRAMEBUFFER, GL_COLOR_ATTACHMENT0, GL_TEXTURE_2D, tex, 0);
```

!!! Note
    If you want to render the whole screen to a textuer of a different size you need to call glViewport each time before rendering to your framebuffer with the new dimensions of your texture. 

Now we also need to create a depth and stencil texture to the framebuffer object. You can make individual attachments for each, or you can combine them into a single 32 bit texture

```cpp
// now we need to create textures for the depth and stencil as well
// we can combine them into a single texture
// the first 24 bits will be depth, the last 8 stencil glTexture2D(  )
glTexImage2D( GL_TEXTURE_2D , 0, GL_DEPTH24_STENCIL8, 800, 600, 
        0, GL_DEPTH_STENCIL, GL_UNSIGNED_INT_24_8, NULL );
glFramebufferTexture2D(GL_FRAMEBUFFER, GL_DEPTH_STENCIL_ATTACHMENT, GL_TEXTURE_2D, tex, 0);
```

## Renderbuffer object attachments

Just like you can attach textures, you can also attach **renderbuffer objects**. This is an actual buffer (an array of bytes, integers, pixels etc), but you can't read from this one directly. This makes them faster than textures, because there are some optimizations that OpenGL can make. You can only read from them with the slow glReadPixels. 

Use render buffers if you never need to sample data from a specific buffer, otherwise use textures. 

It's constructed very similarly to the texture

```cpp
unsigned int rbo;
glGenRenderbuffers(1, &rbo);
glBindRenderbuffer(GL_RENDERBUFFER, rbo);
glRenderbufferStorage(GL_RENDERBUFFER, GL_DEPTH24_STENCIL8, 800, 600);
glBindRenderbuffer(GL_RENDERBUFFER, 0);

// attach the renderbuffer to the depth and stencil 

glFramebufferRenderbuffer(GL_FRAMEBUFFER, GL_DEPTH_STENCIL_ATTACHMENT, GL_RENDERBUFFER, rbo);
```

## Rendering to a texture

1. Render the scene as usual with the new framebuffer bound as active
2. Bind to default framebuffer
3. Draw a quad that spans the entire screen with the new framebuffers' color buffer as its texture. 

!!! Note
    You can put this texture on any object. For example you can render reflections like this. 


## Post-processing

Now that we have all pixels on a texture we can start implementing a compositing pipeline. The effects of this pipeline are called **post-processing** since they are happening after the 3d objects have been rasterized. A quick trick is to invert all the colors:

```GLSL
FragColor = vec4(vec3(1.0 - texture(screenTexture, TexCoords)), 1.0);
```

(There are more examples in the source shader)

## Kernel effects

A **kernel** is a small matrix-like array of values centered on the current pixel that multiplies surrounding pixel values by the kernel values and add them all together to form a single value. Comp kernels usually sum up to 1 if you add all the weights together. If not they will change the luminocity of the image. 

Because everything is now a texture we are simply sampling the image at different uv coordinates. 

```GLSL
const float offset = 1.0 / 300.0;

void main()
{
    vec2 offsets[9] = vec2[](
        vec2(-offset,  offset), // top-left
        vec2( 0.0f,    offset), // top-center
        vec2( offset,  offset), // top-right
        vec2(-offset,  0.0f),   // center-left
        vec2( 0.0f,    0.0f),   // center-center
        vec2( offset,  0.0f),   // center-right
        vec2(-offset, -offset), // bottom-left
        vec2( 0.0f,   -offset), // bottom-center
        vec2( offset, -offset)  // bottom-right
    );

    float kernel[9] = float[](
        -1, -1, -1,
        -1,  9, -1,
        -1, -1, -1
    );

    vec3 sampleTex[9];
    for(int i = 0; i < 9; i++)
    {
        sampleTex[i] = vec3(texture(screenTexture, TexCoords.st + offsets[i]));
    }
    vec3 col = vec3(0.0);
    for(int i = 0; i < 9; i++)
        col += sampleTex[i] * kernel[i];

    FragColor = vec4(col, 1.0);
}
```


There are many interesting kernals out there!








