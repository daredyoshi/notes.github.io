# Math for Game Developers - Character Movement 4

<iframe width="560" height="315" src="https://www.youtube.com/embed/U3mbQrLu7E8" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Now back to moving our character pacman with a vector $\overrightarrow{v}$. Let's say we want to speed up pacman. This is basically going to just require multiplying the vector by a scalar to increase its magnitude. 

$$
2 \overrightarrow{v} = ( \overrightarrow{v}_x * 2, \overrightarrow{v}_y * 2 )
$$

If you want to slow down movement, you can multiply by a fraction, or divide. 

We have implemented this [here](https://github.com/daredyoshi/MathForGameDevelopers/blob/main/005_CharacterMovement4/src/CharacterMovement.cpp)



