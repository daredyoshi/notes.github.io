# Inverse matrices, column space and null space
<iframe width="560" height="315" src="https://www.youtube.com/embed/uQhTuRlWMxw" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

This goes over how to visualize these concepts, not how to calculate them manually. Google "Gaussian elemination" and "Row echelon form" if you are interested in this.

Linear algebra is useful in computer graphics and robotics, but also for helping us solve equations. 

These are **"Linear system of equations"**

$$
2x+5y+3z=-3
$$

$$
4x+0y+8z=0
$$

These have the same format as vectors, and we can use linear algebra to help us solve for them in a matrix like this:

$$
\overbrace{
    \begin{bmatrix}
        2 & 5 & 3 \\
        4 & 0 & 8 \\
        1 & 3 & 0
    \end{bmatrix}
}^A
\overbrace{
    \begin{bmatrix}
        x  \\
        y \\
        z
    \end{bmatrix}
}^{ \overrightarrow{x} }
= 
\overbrace{
    \begin{bmatrix}
        -3 \\
        0 \\
        2
    \end{bmatrix}
}^{ \overrightarrow{v} }
$$

$$
A \overrightarrow{x} = \overrightarrow{v}
$$

There are two ways to use this equation: the most obvious is when $det(A) \not = 0$ this means space is not squished into a single line or dot.


## Inverse Matrices
In this case you can reverse the transformation of the matrix. 

$$
\overbrace{
    A^{-1} 
}^{\text {Inverse Transformation}} 
*
A
=
\overbrace{
    \begin{bmatrix}
       1 & 0  \\
        0 & 1
    \end{bmatrix}
}^{\text{Identity Transform}}
$$

Puts your right back where you started. With this inverse matrix (which you can get from a computer), you can solve the equation for x like so:

$$
A^{-1} A \overrightarrow{x} = A^{-1} \overrightarrow{v}
$$

$$
\overrightarrow{x} = A^{-1}\overrightarrow{v}
$$

But what if the determinant is zero? In this case there is no inverse. But if the vector $\overrightarrow{v}$ is on the line/plane that the zero determinant matrix transfoms into, you can still solve for it. There is terminology for this: 

* Rank 1: A single line matrix (1d)
* Rank 2: A plane matrix (2d)
* Rank 3: A 3d matrix (3d)
* etc.

## Column Space
The set of all possible outputs of a matrix, the "span", is called **column space**. It's the "span" of the columns of the matrix. Rank can be defined as the number of dimensions in the column space. **"Full Rank"** is when the column space has the same dimensions as the matrix rank. The zero vector will always be in ever matrix's column space. 

## Null Space 
When a matrix that is not full rank collapses vectors to the origin (to zero), the space that those vectors were in is called **Null Space**. It's the space of all the vectors that become null because they land on the zero Vector.


