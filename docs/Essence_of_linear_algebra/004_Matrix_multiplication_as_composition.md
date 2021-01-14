# Matrix multiplication as composition

<iframe width="560" height="315" src="https://www.youtube.com/embed/XkY2DOUCWMU" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

*It is my experience that proofs involving matrices can be shortened by 50% if one throws the matrices out* - Emil Artin

Reacap: Linear Transformations are functions with vectors as inputs and vectors as outputs. $L( \overrightarrow{v}  )$. You can think of them visually like when you transform an object in maya, or as squishing the grid of space. The transformation is always done based off the basis vector matrix. Each column in the matrix is one of the basis vectors. Then, if you multiply a vector by that matrix, you are transforming the vector in the space of that matrix. 

## What if you want to apply multiple transformations of space?
Say that you want to first apply a matrix that rotates everything by 90 degrees and then applies a shear? This is commonly referred to as a *composition* of the previous two transformations. It can be described by it's own matrix by following ${\mathbf{\hat{\imath}}}$ and ${\mathbf{\hat{\jmath}}}$. This is esentialyl a new matrix from multiplying the first two.

To make it simple, you can apply things the long way where the vector is first transformed by the rotation matrix and then the shear matrix, but the reslt is identical to the right where the vector is transformed by the composition matrix.

$$
\overbrace{
\begin{bmatrix}
    1 & 1 \\
    0 & 1
\end{bmatrix}}^{\text{Shear Matrix}}
\bigg(
\overbrace{
\begin{bmatrix}
    0 & -1 \\
    1 & 0
\end{bmatrix}}^{\text{Rotation Matrix}}
\begin{bmatrix}
    x \\
    y
\end{bmatrix}
\bigg)
=
\overbrace{
\begin{bmatrix}
    1 & -1 \\
    1 & 0
\end{bmatrix}}^{\text{Composition Matrix}}
\begin{bmatrix}
    x \\
    y
\end{bmatrix}
$$

You can call the matrix on the right the *product* of the two matrices on the left. Note that the order of first rotation (matrix in parenthesis) and then shear is important. This is based on function notation in mathematics. You read the composition from right to left (and the order is important!).


$$
\begin{bmatrix}
    1 & 1 \\
    0 & 1
\end{bmatrix}
\begin{bmatrix}
    0 & -1 \\
    1 & 0
\end{bmatrix}
=
\begin{bmatrix}
    1 & -1 \\
    1 & 0
\end{bmatrix}
$$

To multiply matrices you multiply each column (base vector) in the first matrix $M_1$ by each row in the matrix it is being transformed into $M_2$. 


$$
\overbrace{
   \textcolor{magenta}{
       \begin{bmatrix}
           0 & 2 \\
           1 & 0
       \end{bmatrix}
   } 
}^{M_2}
\overbrace{
    \textcolor{yellow}{
        \begin{bmatrix}
            \textcolor{green}{
                1
            } & \textcolor{red}{
                -2
            }\\
            \textcolor{green}{
                1
            } & \textcolor{red}{
                0
            }
        \end{bmatrix}
    }
}^{M_1}
=
\begin{bmatrix}
    2 & 0 \\
    1 & -2
\end{bmatrix}
$$

$$
\textcolor{magenta}{
    \begin{bmatrix}
        0 & 2\\
        1 & 0
    \end{bmatrix}
}
\textcolor{green}{
    \begin{bmatrix}
        1 \\
        1
    \end{bmatrix}
}
=
\textcolor{green}{
    1
}
\textcolor{magenta}{
    \begin{bmatrix}
        0 \\
        1
    \end{bmatrix}
}
+
\textcolor{green}{
    1
}
\textcolor{magenta}{
    \begin{bmatrix}
        2 \\
        0
    \end{bmatrix}
}
=
\begin{bmatrix}
    2 \\
    1
\end{bmatrix}
$$

$$
\textcolor{magenta}{
    \begin{bmatrix}
        0 & 2 \\
        1 & 0
    \end{bmatrix}
}
\textcolor{red}{
    \begin{bmatrix}
        -2 \\
        0
    \end{bmatrix}
}
=
\textcolor{red}{
    -2
}
\textcolor{magenta}{
    \begin{bmatrix}
        0 \\
        1
    \end{bmatrix}
}
+
\textcolor{red}{
    0
}
\textcolor{magenta}{
    \begin{bmatrix}
        2 \\
        0
    \end{bmatrix}
}
=
\begin{bmatrix}
    0 \\
    -2
\end{bmatrix}
$$

This can be simplified to the following

$$
\begin{bmatrix}
    a & b \\
    c & d
\end{bmatrix}
\begin{bmatrix}
    e & f \\
    g & h
\end{bmatrix}
= 
\begin{bmatrix}
    ae+bg & af+bh \\
    ce+dg & cf+dh
\end{bmatrix}
$$

The order of the matricies matters as this shows!

