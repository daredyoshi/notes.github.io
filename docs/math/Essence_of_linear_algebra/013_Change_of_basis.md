# Change of basis

<iframe width="560" height="315" src="https://www.youtube.com/embed/P2LTAUO1TdA" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

As a review in linear algebra wewe often think of each element in a vector scaling it's corresponding basis vectors. A **Coordinate system** is any way to translate between vectors and sets of numbers. Let's imagine using different basis vectors. 

The same vectors in different coordinate spaces have different numbers. This is kind of like a transformed object space. Note that the origin will always be 0, 0. The direction of axes and spacing of grid lines can all change. So how do we translate between coordinate systems. So the alternative coordinate system might be represented like this:

$$
\begin{bmatrix}
    -1 \\
    2
\end{bmatrix}
=
-1 \overrightarrow{b_1} 
+ 
2 \overrightarrow{b_2}
$$

From the other perspective $b_1$ = is 2, 1 and $b_2$ is -1, -1. You can translate between a strange coordinate system and the normal one by multiplying the vectors by the strange one's vectors. 

$$
-1 
\begin{bmatrix}
    2 \\
    1
\end{bmatrix}
+ 
2 
\begin{bmatrix}
    -1 \\
    1
\end{bmatrix}
=
\begin{bmatrix}
    -4 \\
    1
\end{bmatrix}
$$

The process of scaling each of the basis vectors by the basis vectors of the other coordinate system and then adding them together is basically matrix multiplication (transformation). Matrix multiplication moves things into different coordinate systems. 

To get vectors into another coordinate space, you can multiply them by the inverse of the matrix of that coordinate space. So that's how you chagne coordinate spaces for vectors. 

For matrices it's a bit different. This is very similar to how I solved rotations when I first approached them. 

1. Take a vector (possibly one you would like to rotate)
2. Multiply it by the inverse of it's parent (or the Change of basis matrix). 
3. Apply the transformation
4. Multiply that by the inverse of the change of basis matrix to put it back into the original space. 

This is a very useful technique for rotation things in different spaces (like different joints). It can be simplified to this $A^{-1}MA$






