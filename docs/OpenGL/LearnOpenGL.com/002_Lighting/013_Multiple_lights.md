# Multiple Lights

You can define each light as a function in GLSL, and you can bind multiple lights into struct arrays. 

For arrays you set the properties of each struct in the array like so 

```cpp
glUnfirom3f(glGetUniformLocation(m_ID, "pointLights[0].diffuse", vx, vy, vz));
```

It's a bit annoying, and I worked around it by wrapping it into a function that sets all the elemnts based on a string name of the object. I set the name to "pointLights[0]". This will break really easily though, so I'm looking forward to seeing how a more complex render pipeline is structures. 




