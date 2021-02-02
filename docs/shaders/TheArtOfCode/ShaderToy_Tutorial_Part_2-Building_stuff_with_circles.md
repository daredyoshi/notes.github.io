# Building Stuff with Circles

We can abstract the logic for creating a circle into it's own function:

```cpp
float Circle(vec2 uv, vec2 p, float r, float blur){
    float d = length(uv-p);
    float c = smoothstep(r, r-blur, d);
    
    return c;
}
```

Now how to offset the potiion of the circle? We do this by modifying the uv value and puttin it into a new circle. If we want to add another circle, we need to add or subtract it from the existing circle 

```cpp
float c = Circle(uv, vec2(0, 0), .4, .01);
c -= Circle(uv, vec2(.2, .1), .1, .01);
c -= Circle(uv, vec2(-.2, .1), .1, .01);
```

You can change the color by casting the c mask to col

```cpp
vec3 col = c* vec3(1., 0., 1.);
```

Now that we're making a smiley we might as well make a mouth. One way to do this is to have two circles, one subtracted from another. 

```cpp
// Normalized pixel coordinates (from 0 to 1)
vec2 uv = fragCoord/iResolution.xy;

uv -= .5; // -0.5 <> 0.5
uv.x *= iResolution.x / iResolution.y;

// the head
float c = Circle(uv, vec2(0, 0), .4, .01);

// the mouth
float mouth_y = -.1;
float mouth_height = 0.2;
float mouth_r = .25;

float mouth = Circle(uv, vec2(0, mouth_y), mouth_r, .01);
mouth -= Circle(uv, vec2(0, mouth_y + mouth_height), mouth_r + .05, 0.01);

if(mouth < 0.){ mouth = 0.;}

c -= mouth;


// the eyes
c -= Circle(uv, vec2(.2, .1), .1, .01);
c -= Circle(uv, vec2(-.2, .1), .1, .01);



vec3 col = c* vec3(1., 0., 1.);

// Output to screen
fragColor = vec4(col, 1.0);
```

<iframe width="640" height="360" frameborder="0" src="https://www.shadertoy.com/embed/tltfR8?gui=true&t=10&paused=true&muted=false" allowfullscreen></iframe>



