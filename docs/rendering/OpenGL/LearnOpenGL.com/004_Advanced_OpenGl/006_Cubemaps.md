# Cubemaps

One multi-texture texture is a **cube map**. this is an attempt at a 360 degree texture. It contains 6 individual 2d textures that each form one side of a textured cube. The advantage of a cubemap is that it can be indexed/sampled using a direction vector. 

![cubemap_sampling](./cubemaps_sampling.png)

The direction vector can be imagined as similar to the interpolated local vertex position of the cube. 

## Creating a cube map

The cubemap is created like any other texture, but instead of GL_TEXTURE_2D we use GL_TEXTURE_CUBE_MAP. The order of images is important:

1. right
2. left
3. top
4. bottom
5. front
6. back

```cpp
std::vector<std::string> SKYBOX_TEXTURES{{
    "textures/skybox/right.jpg",
    "textures/skybox/left.jpg",
    "textures/skybox/top.jpg",
    "textures/skybox/bottom.jpg",
    "textures/skybox/front.jpg",
    "textures/skybox/back.jpg"
}};
// load cubemap
// --------------
// cubemaps are loaded just like other textures
unsigned int skyboxID;
glGenTextures(1, &skyboxID);
glBindTexture(GL_TEXTURE_CUBE_MAP, skyboxID); // you need to load each image manually though
int width, height, nrChannels;
unsigned char *data;  
unsigned int count{0};
// load each side of the cube map
for(std::string skyboxTexture : SKYBOX_TEXTURES){
    std::string skyboxTexturePath = CWD + skyboxTexture;
    data = stbi_load(skyboxTexturePath.c_str(), &width, &height, &nrChannels, 0);
    glTexImage2D(
        GL_TEXTURE_CUBE_MAP_POSITIVE_X + count,
        0, GL_RGB, width, height, 0, GL_RGB, GL_UNSIGNED_BYTE, data
    );
    ++count;
}
// specify wrapping and fitering methods
glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_MAG_FILTER, GL_LINEAR);
glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_MIN_FILTER, GL_LINEAR);
glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_S, GL_CLAMP_TO_EDGE);
glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_T, GL_CLAMP_TO_EDGE);
// this is the textures wrapping method in the r coordinate (like z for positions)
glTexParameteri(GL_TEXTURE_CUBE_MAP, GL_TEXTURE_WRAP_R, GL_CLAMP_TO_EDGE);  
```

Unlike regular textures, you need to load an image into each channel. A skybox is a way to make the environment seem much bigger than it is. Cubemaps are usually used for skyboxes and reflections. 

## Displaying the skybox

You need to create a cube to sample from. The cube is get the view matrix of the camera, but always with the position at 0. What's interesting is that when you sample the color for the cube, it's from a 3d vector. GLSL converts the 3d vector into texture coordinates for the skybox. 

Note that if you render the whole skybox first, you are running the fragment shader for fragments that you don't need to. It's better render the skybox last, after the depth buffer has been filled, and only render it on the parts of the depth buffer that are 0.0. The problem is that because the skybox is not large, it will be drawn over all the other objects. We need to trick the depth buffer into thinking the skybox is drawn at 1.0 depth (the maximum). This is done in the vertex shader by setting the z value to it's w value, because the depth is derived by dividing the z by the w. That way we know it's maximum depth, and if we draw the skybox after everything else it will only draw where there is nothing else. 

## Environment Mapping

Now that we have a skybox, we can use this in our shaders using **environment mapping** for *reflection** and **refractions**. 

### Reflection

In vfx Reflection samples from the currently loaded scene, but in GL it has to sample from something prebaked (or draw the entire scene again). With a cubemap you can get pretty good looking static reflections. Basically you get the reflection vector based on the interpolated normal and position, and then sample the cube map with it. 

```GLSL
#version 330 core
out vec4 FragColor;

in vec3 Normal;
in vec3 Position;

uniform vec3 cameraPos;
uniform samplerCube skybox;

void main()
{             
    vec3 I = normalize(Position - cameraPos);
    vec3 R = reflect(I, normalize(Normal));
    FragColor = vec4(texture(skybox, R).rgb, 1.0);
}
```

Refraction is similar, but like reflection of course it won't render the things behind it. You basically slightly bend the vector of the camera position. 


```GLSL
void main()
{             
    float ratio = 1.00 / 1.52;
    vec3 I = normalize(Position - cameraPos);
    vec3 R = refract(I, normalize(Normal), ratio);
    FragColor = vec4(texture(skybox, R).rgb, 1.0);
}
```

## Dynamic Envinronment Maps

While a static maps looks great, you can also generate these maps live based on a sample position and writing things to the frambuffer. This looks great but is very expensive, so the amount of samples is kept as low as possible.
