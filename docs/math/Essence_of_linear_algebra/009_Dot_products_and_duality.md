# Dot products and duality
<iframe width="560" height="315" src="https://www.youtube.com/embed/LyGKycYT2v0" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Traditional Dot product explanation
You multiply each of the rows in two vectors by each other and then add them together:

$$
\begin{bmatrix}
    6 \\
    2 \\
    8
\end{bmatrix}
\cdot
\begin{bmatrix}
    1 \\
    8 \\
    5
\end{bmatrix}
=
6*1 + 2 * 8 + 8 * 5 + 3*3
$$

This has a very nice geometry interpretation though. If two vectors are pointing in generally the same direction, their dot product is positive, perpendicular it's zero, and away it's negative. Order does not matter. 

This only works for normalized vectors. Think of it as projecting a vector onto another vector. If you scale the vector, the projection will be greater or less than 1 and the result inaccurate. You can divide the dot product by the difference in vector scale though, and interestingly you will get the same result. 

$$
(2 \overrightarrow{v} ) \cdot \overrightarrow{w} = 2( \overrightarrow{v} \cdot \overrightarrow{w} )
$$

## And how does this work anyway?

The answer has to do with duality. It has to do with the projection of higher rank vectors to lower rank vectors. The key here isn't to understand how a linear transformation between dimensions works, but that if you have a line of evenly spaced dots in a higher dimension, a linear conversion to a lower dimension will keep them evenly spaced. 

Let's transofrm a vector

$$
\overrightarrow{v}
=
\begin{bmatrix}
    4 \\
    3
\end{bmatrix}
$$

Let's say you have a linear transformation that takes ${\mathbf{\hat{\imath}}}$ to 2 and ${\mathbf{\hat{\jmath}}}$ to 1.

This will transform the vector as such

$$
\overrightarrow{v} = 
\begin{bmatrix}
    4 \\
    3 
\end{bmatrix}
=
4(1) * 3(-2) = -2
$$

This is basically matrix vector multiplication

$$
\begin{bmatrix}
    1 \\
    2
\end{bmatrix}
\cdot
\begin{bmatrix}
    4 \\
    3
\end{bmatrix}
= 
4 * 1 + 3 * 2
$$

Is the same as

$$
\overbrace{
    \begin{bmatrix}
        1 & 2
        \\
    \end{bmatrix}
}^{\text{Transform}}
\overbrace{
    \begin{bmatrix}
        4 \\
        3
    \end{bmatrix}
}^{\text{Vector}}
= 4* 1 + 3*2
$$

This is the same as basically taking the dot product! You are basically taking the matrix of ${\mathbf{\hat{\imath}}}$ and ${\mathbf{\hat{\jmath}}}$ and tilting it on it's side. 

Given unit vector ${\hat{\u}}$, if we "project" each of that vectors' coordinates onto ${\mathbf{\hat{\imath}}}$ and ${\mathbf{\hat{\jmath}}}$ then those will be the exact same number. Conversly projecing the other way around gives the same numbers. Projecting ${\hat{\u}}$ in this way is computationally identical to taking the dot product of ${\hat{\u}}$. This is why taknig the dot product of a unit vector is like projecting it onto a line. 

Now if you scale that unit vector, you are also linearly scaling the result. 

Any time you have a dot product you are basically taking the first vector, flipping it to a lower dimension and then projecting it down. This is what people in math refer to as **duality** - a natrual but surprising correspondence. 

You are creating a 1 row matrix from the first vector and then multiplying the second vector by that matrix. 


