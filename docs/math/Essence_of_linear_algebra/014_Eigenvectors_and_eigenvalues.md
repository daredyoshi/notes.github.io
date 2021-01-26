# Eigenvectors and eigenvalues

<iframe width="560" height="315" src="https://www.youtube.com/embed/PFDu9oVAE-g" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

This topic requires knowlage of [linear transformations](../003_Linear_transformations_and_matrices), [determinants](../006_The_determinant), [linear systems](../007_Inverse_matrices_column_space_and_null_space), and [change of basis vectors](../013_Change_of_basis). 

#### Eigenvectors are the vectors of a transformation that do not change.

Consider some linear transformation in 2 dimensions. 

$$
\begin{bmatrix}
    3 & 1 \\
    0 & 2
\end{bmatrix}
$$

Focus on what this does to a vector and the span of that vector passing through the origin. Most vectors will be knocked off that span during their transformation. It would seem pretty co-incedental if the vector happened to stay on the same line. Two vectors stay on the same line though:

* $\begin{bmatrix} -1 \\ 1 \end{bmatrix}$
* $\begin{bmatrix} 1 \\ 0 \end{bmatrix}$

These vectors are called the eigenvectors. Each one of these has an **eigenvalue** which expresses how much the vector is scaled by a transformation. 

#### Why is this useful?

In rotations this is the axis of rotation. It's much easier to think of rotations as rotating around a value. Note that with rotations the eigenvalue will always be 1 since rotation never stretch or squish anything. 

Eigenvectors are found with this expression, $\overrightarrow{v}$ is the eigenvector to solve for:

$$
\overbrace{
    A
}^{\text{Transformation Matrix}}
\overrightarrow{v}
= 
\overbrace{
    \lambda
}^{\text{Eigenvalue}}
\overrightarrow{v}
$$

The matrix scalar product of the vector must be the same in all cases as any transformation of that vector. The right side is scalar vector multiplication and the left side is matrix vector multiplication. 

You can represent $\lambda$ more intuitively by puttin it into a matrix like so:

$$
\lambda
\overbrace{
\begin{bmatrix}
    1 & 0 & 0 \\
    0 & 1 & 0 \\
    0 & 0 & 1
\end{bmatrix}
}^{I}
$$

$$
A
\overrightarrow{v}
=(\lambda I) \overrightarrow{v}
$$

We can now factor out $\overrightarrow{v}$ from both sides and modify the equasion to look like this:


$$
A \overrightarrow{v} - \lambda I \overrightarrow{v} = \overrightarrow{0}
$$

$$
(A - \lambda I) \overrightarrow{v} = \overrightarrow{0}
$$

$$
det(A - \lambda I ) = \overrightarrow{0}
$$

So we want this matrix times $\overrightarrow{v}$ to equal the $\overrightarrow{0}$. This can be any matrix. 

$$
\overrightarrow{v}  
\begin{bmatrix}
    3- \lambda & 1 & 4 \\
    1 & 5-\lambda & 9 \\
    2 & 6 & 5 - \lambda
\end{bmatrix}
$$

A further proof that a vector is an eigenvector is if the determinant of that vector is 0. You can visualize this as the squishification of that vector always becing zero on the number line. $\text{Squishification} \implies det(A - \lambda I ) = 0$. 

We can write this out in the following where we solve for cases of $\lambda$ where the determinant = 0.

$$
det
\Bigg(
\overbrace{
\begin{bmatrix}
    2 - \lambda & 2 \\
    1 & 3 - \lambda
\end{bmatrix}
}^{A - \lambda I}
\Bigg)
=0.00
$$

Setting $\lambda$ to 1 fulfills those requirements. This means that there is a non-zero vector where $A \overrightarrow{v} = ( \lambda I ) \overrightarrow{v}$. This means that $\overrightarrow{v}$ is an eigenvector and the eigenvalue of $\overrightarrow{v}$ is 1. 

Note that *not every transformation has eigenvalues*. For example a rotation matrix will not have any Eigenvectors. 

$$
det
\Bigg(
\begin{bmatrix}
    - \lambda & -1 \\
    1 & - \lambda
\end{bmatrix}
\Bigg)
= (- \lambda)(- \lambda)-(-1)(1)
= \lambda^2 + 1 = 0 
$$

Another interesting example is a *shear* like this:

$$
det
\Bigg(
\begin{bmatrix}
    1 - \lambda & 1 \\
    0 & 1 - \lambda
\end{bmatrix}i
\Bigg)
= (1 - \lambda)(1 - \lambda) - \overbrace{
    1 * 0
}^{\text{Equals 0, so ignore}}
= \lambda = 1
$$

This lines up with what we know: that all eigenvectors have an eigenvalue of 1. 

A final interesting example is when our basis vectors just so happen to be eigenvectors, which make up a matrix called an **Eigenbasis**. If the numbers on the matrix are diagonal it's a **diagonal matrix**. This also means that all the basis vectors are eigenvectors with the diagonal entries of these matrices being their eigenvalues. 

If you keep multiplying this matrix by itself it will be simply scaling it's values. This makes it very easy to work with. This is why many rotation are transformed into a diagonal matrix and then transformed back. 

Also if you needed to calculate the 100th power of a given matrix, it would be much easier to change to an eigenbasis, compute the number, and then change back with the inverse. 

A great exercise to practice this can be found [here](../Eigenvalue puzzle solution.pdf)
