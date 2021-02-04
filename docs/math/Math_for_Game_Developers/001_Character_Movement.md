# Math for Game Developers - Character Movement ( Points and Vectors )

<iframe width="560" height="315" src="https://www.youtube.com/embed/sKCF8A3XGxQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Character movement is done with vectors $\overrightarrow{v}$. In video games vectors are used for their angles and their magnitude. 

$$
\overrightarrow{v} =
( \Theta , L)
$$

The magnitude of the vector can be used to determine how far the character goes and in what direction. In games we store vectors as a list of floats.

$$
\overrightarrow{v}
= (x, y);
$$

To move the character you use this formula. $\prime$ means "new", so $P\prime$ means "new Position". $ \overrightarrow{v}$ is velocity. 

$$
P\prime = P + \overrightarrow{v}
$$

$$
P\prime = (P_x + \overrightarrow{v}_x, P_y + \overrightarrow{v}_y) 
$$

You can see a very simple program that moves a P vector by a V vector [here](https://github.com/daredyoshi/MathForGameDevelopers/blob/main/CharacterMovement/src/CharacterMovement.cpp)
