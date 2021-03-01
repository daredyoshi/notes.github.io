## Camera

In openGL we move the world, not the camera. For the user, this transformation is more easily thought of as a camera. When we talk about camera space we're talking about all the vertex coordinates within camera object space. This becomes the final space the vertices are transformed into. 

!!! Note
    The positive z-axis is the direction that the camera is looking towards. 

Getting the camera position in easy, we already moved the camera backwards (along the negative z) in the previous chapter. The direction is a bit more tricky. We can get the vector we want by normalizing the camera position - the camera target. 

```cpp
// the position of our camera
glm::vec3 cameraPos {0.0f, 0.0f, 3.0f};
// the direction fo our camera
// tecnically this is pointing in the reverse direction of what it is targeting
glm::vec3 cameraTarget { 0.0f, 0.0f, 0.0f };
glm::vec3 camZ { glm::normalize(cameraPos - cameraTarget) };
// get the right access by crossing up with the direction
glm::vec3 up = glm::vec3(0.0f, 1.0f, 0.0f);
glm::vec3 camX = glm::normalize(glm::cross(up, camZ));
// now get the up vector by crossing the x and z
glm::vec3 camY = glm::normalize(glm::cross(camZ, camX));
```

You can create matricies from their axies and a position vector directly


$$
LookAt = 
\begin{bmatrix}
    R_x & R_y & R_z & 0 \\
    U_x & U_y & U_z & 0 \\
    D_x & D_y & D_z & 0 \\
    0 & 0 & 0 & 1
\end{bmatrix}
* 
\begin{bmatrix}
    1 & 0 & 0 & -P_x \\
    0 & 1 & 0 & -P_y \\
    0 & 0 & 1 & -P_z \\
    0 & 0 & 0 & 1
\end{bmatrix}
$$

In this case R is right (X), U is up (y) and D is direction (z). 

You move everything by multiplying all the vertices by a view matrix in the vertex shader. 

## Time

It's not good using a const value to increment the movement speed. Depending on the system the program is used on, the will vary based on how many redraws there are ber second. 

Because of this, Graphics applications usually keep track of a **deltatime** variable that stores the tim it took to render the last frame. 

## Look around

To look around we're changing the cameraFront vector based on the input of the mouse. The three euler angles we're changing will be the *pitch, yaw* and *roll*. 

![euler_angles](../camera_pitch_yaw_roll.png)

Right now we only care about the yaw and pitch values. Given an angle for yaw, we know that the new vector for it will be 

$$
x = cos(yaw)*  cos(pitch)
y = sin(yaw)
z = sin(yaw) * cos(pitch)
$$

This will make a lot more sense after learning about more trigonometry. We use mouse input to get pitch and yaw. 

