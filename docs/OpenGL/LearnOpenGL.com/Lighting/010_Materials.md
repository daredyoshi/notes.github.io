# Materials

Different surfaces can have common properties abstracted into **material properties**. You can define a struct of material properties in your material like so:

```GLSL
#version 330 core
struct Materia{
    vec3 ambient;
    vec3 diffuse;
    vec3 specular;
    float shininess;
};

uniform Material material;
```

There is no big different to storing all these as individual unforms, but a struct is more organized. Here we have defined a color for each of the phong lighting's components. 

If we plug this into our object, it looks a bit too bright. The lights need the same struct, and they multiply those parts of the material in the shader.


