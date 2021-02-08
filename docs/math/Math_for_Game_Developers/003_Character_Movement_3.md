# Math for Game Developers - Character Movement 3 (Vector Length)

<iframe width="560" height="315" src="https://www.youtube.com/embed/bk-RyG0KR_I" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Now we want to know how far pinkey has to go to find packman. We already have the vector that pinky has to dravel on to get to pacman, and we are not going to find the distance that pinky has to now travel at this angle. o

If you remember from the first video, the vector can be expressed in two ways. The angle and length, or (x,y). Because we aren't representing the vector with the length part, we need to deduct the length. This is pretty easy to do by creating a triangle with x and y and using the pythagorean theorem to get the length of the imaginary third side. 

$$
a^2 + b^2 = c^2
$$

$| \overrightarrow{v} |$ stands for the vector's length. So we can say:


$$
| \overrightarrow{v} |^2 = \overrightarrow{v}_x^2 + \overrightarrow{v}_y^2
$$


$$
| \overrightarrow{v} | = \sqrt{x^2 + y^2}
$$

This will find us the distance between piny and pacman. You can find the implementation [here](https://github.com/daredyoshi/MathForGameDevelopers/blob/main/CharacterMovement3/src/CharacterMovement.cpp)



