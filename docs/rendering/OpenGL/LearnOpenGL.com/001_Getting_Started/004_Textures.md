# Textures

Textures require uv coordinates. Each vertex should have a **textuer coordinate** associated with it that specifies which part of the texuture image to sample from. Textures coordinates range from 0 to 1 in the x and y axis (as long as we use 2d textures). You retrieve the correct color for a pixel by **sampling** the images at the texture coordinate. Lower left is 0, 0 and upper right is 1, 1. 

### Texture Wrapping

What happens if we sample a texture outside of it's range? The default behaviour is so repeat textures images, but there are more options

* ```GL_REPEAT``` Repeat the image: this is the default
* ```GL_MIRRORED_REPEAT``` Repeat the image but mirror each time for simple seamlessness
* ```GL_CLAMP_TO_EDGE``` Clamps the coordinates between 0 and 1. This extends/stretches the textures. 
* ```GL_CLAMP_TO_BORDER``` Any samples outside the border are given a user-specified color (in the case of decalse this can have an alpha of 0)

These options aer set with 

```cpp
glTexParameter(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_MIRROR_REPEAT);
glTexParameter(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_MIRROR_REPEAT);
```

Note that you need one argument for each axis. If you are using the border extend color you specify the border color like this

```cpp
float borderColorp[] = { 1.0f, 1.0f, 0.0f, 1.0f };
glTexParameterfv(GL_TEXTURE_2D, GL_TEXTURE_BORDER_COLOR, borderColor);
```

### Texture Filtering

Since texture coordinates can be any floating point value, OpenGL needs to figure out which texture pixel (**textel**) is at the coordinate. There are different ways to subsample textures, known as **texture filtering**. These are the two most important ones

* ```GL_NEAREST``` aks **nearest neighbor** or **point** filtering is the default method. This is a proper pixel sample with no additional interpolations. 

*  ```GL_LINEAR``` aka **(bi)linear filtering** blurs the result based on the pixels around the edges the of the pixel the sample is closes to. 

The below image show the difference on a very low resolution image

[!textureFiltering](./texture_filtering.png)

You set the texture filtering options of your texture like this

```cpp
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST); // set the filtering option for minimizing a texture
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR); // set the filtering mode for maximizing a texture
```

### Mipmaps

When there are thousands of objects that are using a texture, there will be some that take up much less or more space on screen than the texture image covers. Not only does OpenGL have difficulties retrieving the right color value when the texture is much higher resolution than the fragment, it is also not memory efficient to load high resolution textures when you don't need to. To solve this issue **mipmaps** are created by OpenGL which are basically a collection of texture images where each subsequent texture is twice as small compared to the previous one. 

These are created by ```glGenerateMipmaps``` after creating a textures. The following options are avaliable:

* ```GL_NEAREST_MIPMAP_NEAREST```: Takes the nearest mipmap to match the pixel size and uses neighbor interpolation for texture sampling
* ```GL_LINEAR_MIPMAP_NEAREST``` : Same as above but using linear interpolation
* ```GL_NEAREST_MIPMAP_LINEAR```: Linearly interpolates between the two mipmaps that most closely match the size of a pixel, and samples the result with linear interpolation. 
* ```GL_LINEAR_MIPMAP_LINEAR```: Linearly interpolates between the two most close mipmaps and then samples the result with linear interpolation as well. 

You set these like this

```cpp
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_LINEAR_MIPMAP_LINEAR);
glTexParameteri(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
```

!!! note 
    It's dumb to set the magnification filter to mipmapping, since mipmaps don't go above the scale of the texture image. Fortunately this will generate a GL_INVALID_ENUM error code. 

### Loading and creating textures

How do you actually read a texture? One way is to write our own image loader based on a certain file format. Why do this though when we can use an image-loading library that supports several popular formats?

A popular one is stb_image.h


```cpp

Texture::Texture(const std::string texturePath){
    // load the texture into a char stream
    int width{0};
    int height{0};
    int nrChannels{0};
    unsigned char *data = stbi_load(texturePath.c_str(), &width, &height, &nrChannels, 0);

    // Like any open gl objects, textures are referenced with an id
    // the first input is how many textures to generate and the second
    // is an int array with the ids (only a single id in our case)
    glGenTextures(1, &m_ID);
    
    // now bind the texture (this makes it the current object other things operate on)
    glBindTexture(GL_TEXTURE_2D, m_ID);

    // now generate the textuer using the previously loaded data
    //
    // The first argument sets the target. This means that GL_TEXTURE_1D or GL_TEXTURE_3D
    // will not be effected by this call
    //
    // The second argument specifies the mipmap level to generate
    // The third argument is what sort of format we want to tsore the textuer in
    // The next two images set the pixel width and height of the resulting texture.
    // The next argument should always be 0 (legacy)
    // The 7th and 8th specify the format and datatype of the source image. We loaded
    // the textuer as RGB values and stored them as chars (bytes) so we'll pass the
    // corresponding values. 
    // The last is the actual image data
    glTexImage2D(GL_TEXTURE_2D, 0, GL_RGB, width, height, 0, GL_RGB, GL_UNSIGNED_BYTE, data);
    // Generate the mipmaps to the level previously specified
    glGenerateMipmap(GL_TEXTURE_2D);

    // stbi comes with it's own wrappers for releasing pointer data
    stbi_image_free(data); 

}

```


Then you need to bind the texturer before drawing elements

```
glBindTexture(GL_TEXTURE_2D, texture);
glBindVertexArray(VAO);
glDrawElements(GL_TRIANGLES, 6, GL_UNSIGNED_INT, 0);
```

In the shader we reference the texture with a uniform sampler2D

```GLSL
uniform sampler2D ourTexture;
void main()
{
    FragColor = texture(ourTexture, TexCoord);
}
```



### Texture Units

What is the uniform in the frag shader doing? It's telling the program which texture to sample. Using glUniform1i we can actually assign a lcoation value to the texture sampler so we can set multiple textures at once in the fragment shader. This location is commonly known as the **texture unit**, and it defaults to 0, which is the default active texture unit.


There are at least 16 texture units 

```cpp
glActiveTexture(GL_TEXTURE0); // activate the unit
glBindTexture(GL_TEXTURE_2D, texture); // bind the texture to the unit
```




