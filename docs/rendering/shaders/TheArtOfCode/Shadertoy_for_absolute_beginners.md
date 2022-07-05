# Shadertoy for absolute beginners

In this tutorial we will make a circle. A shader is a little program that takes some input and output just a pixel color. A pixel color consists of three float ( between 0 and 1 ). Then there is also the alpha channel, but shadertoy doesn't really use for anything. Shadertoy takes the output variables fragColor and assigns a vec4 (rgba) to it. 

The uv is an input for the shader to figure out what color to output. For example you can create a gradient from left to right from black to red by assigning the uv.x to the r component of the fragColor. You calculate the uv based on the pixel coordinate, which depends on the size of the screen. The Uv is the normalized pixel coordinate. 

```cpp
vec2 uv = fragCoord/iResolution.xy;
```

Let's figure out how to draw a circle. We can do this by picking a point on the screen, and compare the distance to that point to figure out if the pixels are in or out of the circle. We then use a smoothstep to soften the edges of the circle. 

```cpp
uv -= .5; // -0.5 <> 0.5
uv.x *= iResolution.x / iResolution.y;
float d = length(uv);
float r = 0.3;


float c = smoothstep(r, r-0.01, d);


// Output to screen
fragColor = vec4(vec3(c), 1.0);
```

<iframe width="640" height="360" frameborder="0" src="https://www.shadertoy.com/embed/wttfR8?gui=true&t=10&paused=true&muted=false" allowfullscreen></iframe>
