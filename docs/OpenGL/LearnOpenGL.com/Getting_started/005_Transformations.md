This lesson is in general a review of the material I have covered [here](../../../math/Essence_of_linear_algebra)

A couple of highlights

!!! Homogeneous coordinates
    The fourth compoenent of a vector is known as the **homogeneous coordinate**. To solve the 3d vector of a vector like this we divide the x, y and z coordinates by this w coordinate. Usually this is assumed to be 1.0, but it's useful when setting the perpective of a camera. If it's zero the vector cannot be translated and is known as a *dicrection vector**. It's also used for creating 3d perspective. 

### Rotation

In 2d and 3d rotations are represented with **angles** or radians. 

!!! Angles and Radians
    You can easily convert back and forth between them
    angle in degrees = angle in radians * (180 / PI)
    angle in radians = angle in degrees * (PI / 180)
    Where PI equals (rounded) 3.14159265359. 

In 3d rotation are specified with an angle and a **rotation axis**. You can do this with math using trigonometry. Here are some common forumulas

Rotation around x-axis

$$
\begin{bmatrix}
    1 & 0 & 0 &0 \\
    0 & \cos{\theta} & -\sin\theta & 0 \\
    0 & \sin\theta & \cos\theta & 0 \\
    0 & 0 & 0 & 1
\end{bmatrix}
* 
\begin{pmatrix}
    x \\
    y \\
    z \\
    1 
\end{pmatrix}
= 
\begin{pmatrix}
    x \\
    \cos\theta * y - \sin\theta * z \\
    \sin\theta * y + \cos\theta * z \\
    1
\end{pmatrix}
$$

It's best not to simple rotate about axes though, as this quickly introduces a problem called **Gimbal lock**. It's much better to rotate about an arbitrary axis. 

Ideally you will base your rotations around quaternions.

###GLM 

GLM is the Open**Gl M**athematics library and has a bunch of useful matrix functions. 

```
#include <glm/glm.hpp>
#include <glm/gtc/matrix_transform.hpp>
#include <glm/gtc/type_ptr.hpp>
```




