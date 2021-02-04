# Character Movement 2 (Subtracting Vectors)

<iframe width="560" height="315" src="https://www.youtube.com/embed/WNaxtPTMqSo" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

We now have two characters that we are thinking about: pacman and pinky the ghost. We want to create a vector between pinky and pacman so that pinkey can eat packman. This will be vector $ \overrightarrow{v}$. The formula for this is very straightforward:

$$
\overrightarrow{v} = P - I
$$

Notice how the pacman point comes first. This is because otherwise the vector will be pointing the other way. 

$$
\overrightarrow{v} = (P_x - I_x, P_y - I_y);
$$

We've Implemented this [here](https://github.com/daredyoshi/MathForGameDevelopers/blob/main/CharacterMovement2/src/CharacterMovement.cpp)


