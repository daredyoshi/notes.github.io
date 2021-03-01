# Mouse Control (Euler Angles)

Euler angles were developed by Leonhad Euler. They can specify any 3d rotation with just three values. 

There are three rotations in euler angles:

* Pitch (up/down)
* Yaw (left/right)
* Roll (tilting)

When the player moves the mouse up and down that's the pitch, left and right is the yaw. So we want to convert these angles to a vector. This conversion is not hard to do with trigonometry.

Take a right triangle with a hypotenuse of 1, and an angle of $\theta$. In this case $sin\theta = \frac{y}{1}$ as well as this $\cos\theta = \frac{y}{1}$. 

So to find each element of the vector

$$
V_x = cos(Y) * cos(P)
$$

$$
V_y = sin(P)
$$

$$
V_z = sin(Y) * cos(P)
$$

You need to multiply x and z by the cosign of the pitch so that they become less as you look straight up or straight down. 
