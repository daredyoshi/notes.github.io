# Math for Game Developers - Distance Comparison

<iframe width="560" height="315" src="https://www.youtube.com/embed/DxmGxkhhluU" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

 How should we go about comparing the distance between two points (C and I) to our P (position?) if we want to find out which one is closer? 

 $$
 | \overrightarrow{CP} | ? | \overrightarrow{IP}
 $$


$$
\sqrt{
    \overrightarrow{CP_x}^2 + \overrightarrow{CP_y}^2
}
?
\sqrt{
    \overrightarrow{IP_x}^2 + \overrightarrow{IP_y}^2
}
$$

We can unsqure these to make it a simpler equation. 


$$
\overrightarrow{CP_x}^2 + \overrightarrow{CP_y}^2 
?
\overrightarrow{IP_x}^2 + \overrightarrow{IP_y}^2
$$

So it's actually quicker to compare the squares of the vectors, than the square roots of the squares of the vectors. We implement this [here](https://github.com/daredyoshi/MathForGameDevelopers/blob/main/004_DistanceComparison/src/CharacterMovement.cpp)



