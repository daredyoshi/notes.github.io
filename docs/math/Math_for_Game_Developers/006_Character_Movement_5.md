# Math for Game Developers - Character Movement 5 (Unit-Length Vectors)

<iframe width="560" height="315" src="https://www.youtube.com/embed/hh-3xLawoYo" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

One thing about the original pacman, was that he always looked into the same direction as he was moving. So now let's make him moving while looking at pinky. One difference to the vector that stores the velocity of P, the lookat vector has to be a **unit vector**, this means that it's magnitude needs to be 1. It can face anywhere it wants to, but it's magnitude or length will be on. So how do we convect a vector into a unit vector?

Let's calculate the direction between P and I $\overrightarrow{PI}$. A unit vector is represented with ^, so what we're solving for is $\widehat{\overrightarrow{PI}}$. To normalize a vector (the process of making a vector length 1), we divide it by it's length.  

$$
\widehat{\overrightarrow{PI}} = 
\frac{
    \overrightarrow{PI}
}{
    | \overrightarrow{PI} | 
}
$$

$$
\widehat{ \overrightarrow{PI} } =
\Bigg(
\frac{
    \overrightarrow{PI}_x
}{
    | \overrightarrow{PI} |   
},
\frac{
    \overrightarrow{PI}_y
}{
    | \overrightarrow{PI} |
}
\Bigg)
$$

Unit length vectors are extremely useful in game development. Knowing how to create and use them will help you everywhere in game development. We have implemented it [here](https://github.com/daredyoshi/MathForGameDevelopers/blob/main/006_CharacterMovement5/src/CharacterMovement.cpp)

