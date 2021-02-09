# Math for Game Developers - Backstabbing (Dot Product)

<iframe width="560" height="315" src="https://www.youtube.com/embed/Q9FZllr6-wY" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

If you want to calculate whether your player is stabbing another black in the back, you can use dot products. A dot product takes in two unit vectors. In this case $\widehat{\overrightarrow{v}}$ is the direction the enemy is facing and $\widehat{ \overrightarrow{BR} }$ the direction from the enemy to the player. Just to review, in order to get $\widehat{ \overrightarrow{BR} }$ we use the following formula:

$$
\widehat{
\overrightarrow{BR}
}
= 
\frac{
    R-B
}{
    |R-B|
}
$$

So now that we have our vectors, we want to know if they are facing in the same direction, or opposite directions. We want to test whether or not the player is behind the enemy. We can do this with the dot produt. The dot product can be thought of as a relationship between two vectors. Let's start with the formula for it: 

$$
| \overrightarrow{a} | | \overrightarrow{b} | \cos \theta
$$

Here's a more code friendly formula:

$$
\overrightarrow{a}_x \overrightarrow{b}_x + \overrightarrow{a}_y \overrightarrow{b}_y
$$

This is how it's used in games/engineering. The important thing to know is that if the vectors are facing the same direction, the dot product is going to be 1, opposite direction -1. Using the dotproduct of two vectors we can safely assume that a backstab is valid if the value is less than a certain value.  


$$
\widehat{ \overrightarrow{BR} } \uparrow \cdot
\widehat{ \overrightarrow{V} } \uparrow = 1
$$

$$
\widehat{ \overrightarrow{BR} } \uparrow \cdot
\widehat{ \overrightarrow{V} } \rightarrow = 0
$$

$$
\widehat{ \overrightarrow{BR} } \uparrow \cdot
\widehat{ \overrightarrow{V} } \downarrow =1
$$

Read more about the dot product [here](../vector.md)




