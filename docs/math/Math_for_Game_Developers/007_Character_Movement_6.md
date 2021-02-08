# Math for Game Developers - Character Movement 6

<iframe width="560" height="315" src="https://www.youtube.com/embed/Rcbjmt35PDo" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Pacman never moves diagonally, but we can make this happen. How does this work? We can do this by adding vectors. In this case let's use a vector pointing right $\overrightarrow{R}$ and a vector pointing down $\overrightarrow{D}$ to create a diagonal vector $\overrightarrow{v}$. 


$$
\overrightarrow{D} + \overrightarrow{R} = \overrightarrow{V}
$$

$$
\overrightarrow{V} = (\overrightarrow{D}_x + \overrightarrow{R}_x, 
\overrightarrow{D}_y + \overrightarrow{R}_y
)
$$

This works the same way for subtractions. You can find the implementation [here](https://github.com/daredyoshi/MathForGameDevelopers/blob/main/007_characterMovement6/src/CharacterMovement.cpp)




