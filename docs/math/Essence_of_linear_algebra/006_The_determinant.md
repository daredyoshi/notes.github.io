# The determinant

<iframe width="560" height="315" src="https://www.youtube.com/embed/Ip3X9LOh2dk" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

*"The purpose of computation is insight, not numbers." - Richard Hamming

## How do we measure how much a transformation stretches space?

Or squishes. How much is the area of the basis vectors increased or decreased?

$$
\begin{bmatrix}
    3 & 0 \\
    0 & 2
\end{bmatrix}\\
$$

$$
New area = 3 * 2 = 6
$$

Just because a matrix is sheared doesn't mean that it's area necessarily changes. The area is determined by the length of the vectors in the base coordinate space. You can measure the factor of scale through the **determinant** of a transform. This is the amount that the 1x1 square in each of the basis vectors has changed area. 

$$
det
\bigg(
\begin{bmatrix}
    3 & 2 \\
    0 & 2
\end{bmatrix} 
\bigg)
= 6
$$

The determinant of a matrix is zero if it squishes all of space down to a line. (If the vectors are pointing in the same direction or a point)

## The determinant can be negative

This has to do with the concept of orientation. This happens when a transform "inverts" the orientation of space. For example of X is now pointing in the y direction. On way that this could happen is that if instead of being to the left of ${\mathbf{\hat{\imath}}}$, ${\mathbf{\hat{\jmath}}}$ is now to the right of ${\mathbf{\hat{\imath}}}$. This inverts the orientation of space. This will create a negative determinant. 

# In 3d
You are scaling volumes. It helps to visualize the cube resting on the basis vectors. After transformation this cube will get warped, possibly into a **parallelepiped** if the cube is skewed.

$$
det
\Bigg(
\begin{bmatrix}
    1.0 & 0.0 & 0.5 \\
    0.5 & 1.0 & 0.0 \\
    1.0 & 0.0 & 1.0
\end{bmatrix}
\Bigg)
=\text{Volume of parallelepiped} 
$$

## How do we actually compute the determinant?

$$
det
\Bigg(
\begin{bmatrix}
    a & b \\
    c & d
\end{bmatrix}
\Bigg)
= ad - bc
$$

a and d in this example are the distance that ${\mathbf{\hat{\imath}}}$ and ${\mathbf{\hat{\jmath}}}$ in their primary axes (x and y). Even if only one of their non-primary axes are zero (in the case of a parallelogram) the area is still their base times height. B * C basically tells you how much the parallelograph is squished into either direction. 

In 3d you subtract the det of the y axis and then add the determinant of the z axis.


$$
det
\Bigg(
\begin{bmatrix}
    a & b & c \\
    d & e & f \\
    g & h & i
\end{bmatrix}
\Bigg)
= det_a \Bigg(
\begin{bmatrix}
    e & f \\
    h & i
\end{bmatrix}
\Bigg)
- deb_b \Bigg(
\begin{bmatrix}
    d & f \\
    g & i
\end{bmatrix} \Bigg)
 + det_c \Bigg(
 \begin{bmatrix}
     d & e \\
     g & h
 \end{bmatrix}
 \Bigg)
$$

He doesn't go into exactly why this is because it's not *that* relevant to linear algebra. 

A fun quiz (explain why this makes sense):

$$
det(M_1 M_2) = det(M_1)det(M_2)
$$

This makes sense because you are multiplying the numbers both ways, and the determinant is also multiplied when multiplying matrices.  $6 == 3 * 2$


