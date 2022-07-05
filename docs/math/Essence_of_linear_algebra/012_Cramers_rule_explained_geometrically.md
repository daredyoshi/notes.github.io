# Cramer's rule, explained geometrically

<iframe width="560" height="315" src="https://www.youtube.com/embed/jBsC34PxzoM" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Here we will look at the geometry behind "Cramer's Rule"
The background for this requires [determinants](../006_The_determinant), [dot products](../009_Dot_products_and_duality), and [how to balance equations](../007_Inverse_matrices_column_space_and_null_space)

Cramer's rule helps us solve this equation for $\overrightarrow{xyz}$ :

$$
\begin{bmatrix}
    -4 & 2 & 3 \\
    -1 & 0 & 2 \\
    -4 & 6 & -9 
\end{bmatrix}
\begin{bmatrix}
    x \\
    y \\
    z
\end{bmatrix}
= 
\begin{bmatrix}
    7 \\
    -8 \\ 
    3
\end{bmatrix}
$$

By divding the determinants of new matrices created with $\overrightarrow{xyz}$ and the known transform with the determinant of the known transofrm. But how does this work


$$
x =
\frac{
    det
    \Bigg(
    \begin{bmatrix}
        \textcolor{red}{
            7
        } & 2 & 3 \\
        \textcolor{red}{
            -8
        } & 0 & 2 \\
        \textcolor{red}{
            3
        } & 6 & 9
    \end{bmatrix}
    \Bigg)
}{
    det
    \Bigg(
    \begin{bmatrix}
        -4 & 2 & 3 \\
        -1 & 0 & 2 \\
        -4 & 6 & -9
    \end{bmatrix}
    \Bigg)
}
\qquad
y = 
\frac{
    det
    \Bigg(
    \begin{bmatrix}
        -4 & \textcolor{red}{
            7
        } & 3 \\
        -1 & \textcolor{red}{
            -8
        } & 2 \\
        -4 & \textcolor{red}{
            3
        } & -9
    \end{bmatrix}
    \Bigg)
}{
    det
    \Bigg(
    \begin{bmatrix}
        -4 & 2 & 3 \\
        -1 & 0 & 2 \\
        -4 & 6 & -9
    \end{bmatrix}
    \Bigg)
}
\qquad
z = 
\frac{
    det
    \Bigg(
    \begin{bmatrix}
        -4 & 2 & \textcolor{red}{
            7
        }\\
        -1 & 0 & \textcolor{red}{
            -8
        }\\
        -4 & 6 & \textcolor{red}{
            3
        }
    \end{bmatrix}
    \Bigg)
}{
    det
    \Bigg(
    \begin{bmatrix}
        -4 & 2 & 3 \\
        -1 & 0 & 2 \\ 
        -4 & 6 & -9
    \end{bmatrix}
    \Bigg)
}
$$

General disclaimer: Cramer's rule isn't the best way to compute solutions to linear systems of equations. Gaussian Elimination will always be faster. 

Let's start with the setup. 

$$
3x + 2y = -4
$$

$$
1x + 2y = -2
$$

You can think of this setup geometryically as a certain known matrix transforming an unknown vector where you know what the output is going to be. It can be thought of as a puzzle: Which input vector xy is going to land at output vector -4, -2. Note that if te determinant is zero then this changes things, so we are assuming that the $det(A) \not = 0$. 

$$
\begin{bmatrix}
    3 & 2 \\
    -1 & 2
\end{bmatrix}
\begin{bmatrix}
    x \\
    y
\end{bmatrix}
=
\begin{bmatrix}
    -4 \\
    -2
\end{bmatrix}
$$

To solve this you might try to take the dot product of the input and output transformations for each axis, assuming they are equal. In this case it would be easy to get the transformation we need because we know what the output vectors are. This would only work for vectors that are perpendicular to each other. Transformations that **do** preseve dot products where this would work are called **"Orthonormal"**. These are commonly used for rotation matrices, with no stretching or squishing or flipping. i

$$
\overbrace{
    \begin{bmatrix}
        cos(30°) & -sin(30°) \\
        sin(30°) & cos(30°)
    \end{bmatrix}
}^\text{(Orthonormal)}
\begin{bmatrix}
    x \\
    y
\end{bmatrix}
=
\begin{bmatrix}
    1 \\
    2
\end{bmatrix}
$$

Can be easily solved like this:

$$
x = \begin{bmatrix}
    1 \\
    2
\end{bmatrix}
\cdot
\begin{bmatrix}
    cos(30°) \\
    sin(30°)
\end{bmatrix}
$$

$$
y = 
\begin{bmatrix}
    1 \\
    2
\end{bmatrix}
\cdot
\begin{bmatrix}
    -sin(30°) \\
    cos(30°)
\end{bmatrix}
$$

For all other types of transforms, might there be a similar alternate geometric understanding for the coordinates of our input vector?
Let's start by looking at the area of the parrallelogram of the desired transformation and it's basis vector. This is alwasy exqual to the value of the desired vector. $Area = x$
Thinking of this in terms of area let's us compare it to the area of the vector that we already have. 
In 3d this would be the parallelopiped that the zaxis creates with the basis vectors ${\mathbf{\hat{\imath}}}$ and ${\mathbf{\hat{\jmath}}}$. 

$$
z = det \Bigg(
\begin{bmatrix}
    1 & 0 & x \\
    0 & 1 & y \\
    0 & 0 & x
\end{bmatrix}
\Bigg)
$$

The volume here is also simply the value of the z axis of the vector. 
Note that because the volume is dependant on the order of the vectors (right hand rule) because it determines the sign of the volume. 

Now that we are thinking of these in terms of area, we can observe that these areas change after transformations. Because of how the determinant works, all the areas of all the axes are scaled by the same amount during a transformations. Given A is the transformation matrix $det(A)$.

This allows us to now say that *Signed Area* = $det(A)y$  - *SO* with this we can now solve for y like this

$$
y = 
\frac{
    \text{Area}
}{
    det(A)
}
$$

And we have the area from the point where the input vector lands. And this is how we can use Cramer's rule to figure out which input vector was used to generate an output vector given a matrix. 


$$
y = 
\frac{
    det
    \Bigg(
    \begin{bmatrix}
        -4 & \textcolor{red}{
            7
        } & 3 \\
        -1 & \textcolor{red}{
            -8
        } & 2 \\
        -4 & \textcolor{red}{
            3
        } & -9
    \end{bmatrix}
    \Bigg)
}{
    det
    \Bigg(
    \begin{bmatrix}
        -4 & 2 & 3 \\
        -1 & 0 & 2 \\
        -4 & 6 & -9
    \end{bmatrix}
    \Bigg)
}
$$







