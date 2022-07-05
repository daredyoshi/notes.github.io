# Blending

**Blending** in OpenGL is most well known for being used to implement **transparency** within objects. Transparency blends object with object behind them, and you blend the fragments together. 

The amount of transparency is defined by its color's **alpha** value. This is the fourth component of the color vector that we've seen quite a bit so far. 

## Discarding Fragments

A very cheap and easy way to add transparency is to simply discard fragments that are not a certain alpha. This is commonly known as "cutout" transparency. Grass is a good example for this. When adding vegetation to a scene, we don't want to see a square image of grass, but the actual grass. What we want is to **discard** the fragments that show the transparent parts of the texture, and to not store those in the color buffer. You do this in the shader with a special keyword:

```GLSL
vec4 texColor = texture(texture1, TexCoords);
if(texColor.a < 0.1)
    discard;
FragColor = texColor;
```

!!! Note
    Because textures wrap by default if you have any mipmapping the top of the textuer could wrap to the bottom which looks bad when repeating. Remember you can turn off repeating with glTexParameteri

## Blending

To enable rendering images with different levels of transparency, we need to enable **blending**

```cpp
glEnable(GL_BLEND);
```

Blending in OpenGL happens with the following equation:

$$
C_{screen} = C_{source} * F_{source} + C_{destination} * F_{destination}
$$

After the fragment shader has run and all the tests have passed, this **blend equation** operates on the final color. The way the colors are blended is determined by the glBlendFunc. This function takes two parameters that set the **source factor** and **destination factor** respectively. These are some of the most common options:

```cpp
 // The first factor is how the source is alpha-ed
 // the second is how the color in the buffer is alphad. 
glBlendFunc(GLenum sfactor, GLenum dfactor)
```

| Option | Value |
| -- | -- | 
| GL_ZERO | Factor is 0 | 
| GL_ONE |  Factor is 1 | 
|GL_SRC_COLOR | Factor is equal to the source color vector $C_{source}$ |
| GL_ONE_MINUS_SRC_COLOR |  Factor is equal to 1 - source color $1 - C_{source}$ |
| GL_DST_COLOR | Factor is equal to the destination color $C_{destination}$ | 
| GL_ONE_MINUS_DST_COLOR | $1 - C_{destination}.rgb$ | 
| GL_SRC_ALPHA | Factor is equal to the source alpha value |
| GL_ONE_MINUS_SRC_ALPHA | $1 - C_{source}.a$ | 
| GL_DST_ALPHA | Factor is equal to the destination alpha value | 
| GL_ONE_MINUS_DST_ALPHA | $1 - C_{destination}.a$ | 


For our two square example, we want the alpha of the source color for the source factor and 1 - alpha for the destination color. 

```cpp
glBlendFunc(GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA);
// this does the same as above
glBlendFuncSeperate(GL_SRC_ALPHA, GL_ONE_MINUS_SRC_ALPHA, GL_ONE, GL_ZERO);
```

You can go further and also set the operator for how the color should be factored together:

| Option | Value |
| -- | -- |
| GL_FUNC_ADD | (default) add both colors to each other |
| GL_FUNC_SUBTRACT | subtract both colors from each other SRC - DST | 
| GL_FUNC_REVERSE_SUBTRACT | subtracts the other way DST - SRC | 
| GL_MIN | takes the component-wise minimum of both colors | 
| GL_MAX | takes the component-wise maximum of both colors | 

Usually we can skip setting this because we will be using GL_FUNC_ADD in most situations. 

Adding transparency to our scene works, but the depth buffer is messing stuff up. This is because even though the transparent part should show the windows behind it, the depth test discards that. This is where the depth buffer will not magically fix everything. 

To fix this we need to draw object in order from farthest to closest. A simple way to do this is to measure the distance of objects from the viewer's perspective. We can store this in a **map**data structure from the std lib. 

I sorted the position vector like this:

```cpp
std::sort(vegetation.begin(), vegetation.end(), [&](glm::vec3 p1, glm::vec3 p2){
        return glm::distance(cam.getPos(), p1) > glm::distance(cam.getPos() , p2);
});
```

Not foolproof by any means, sorting is also expensive. There are more advance transparency techniques that tackle this like **order indpendent transparency**. 

 



