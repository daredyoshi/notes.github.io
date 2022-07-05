<iframe width="560" height="315" src="https://www.youtube.com/embed/kYB8IZa5AuE" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

##Linear transfomration
Every transformation is also a function. In linear algebra we most commonly have transformations operating on vectors. Transformation sugguests that you think using *movement*. You imagine the input vector(s) *moving* to the output vectors. To imagine the transfomration as a whole, you imagine every single input vector being transformed. Visualizing this with a grid is handy, and gives the feeling of transforming space itself.

#### A transformation is linear if it has 2 properties:

1. Lines remain lines
2. Origin must remain fixed in place

In General linear transformations keep grid lines paralell. 
To plot a vector you use the same math, but with transformed basis vectors ${\mathbf{\hat{\imath}}}$ and ${\mathbf{\hat{\jmath}}}$. 

$$
{\overrightarrow{V}}
=
\begin{bmatrix}
    -1 \\ 2   
\end{bmatrix}
=
-1 {\mathbf{\hat{\imath}}}+ 2 {\mathbf{\hat{\jmath}}}
$$

$$
Transformed  
{\overrightarrow{V}}
= -1(Transformed  {\mathbf{\hat{\imath}}}) + 2(Transformed  {\mathbf{\hat{\jmath}}})
$$

Given that 

$$
{\mathbf{\hat{\imath}}} = \begin{bmatrix}
    1 \\
    2
\end{bmatrix}\quad and \quad {\mathbf{\hat{\jmath}}} =  
\begin{bmatrix}
    3 \\
    0
\end{bmatrix}
$$

$$
\overrightarrow{V}= -1
\begin{bmatrix}
    1 \\ 2
\end{bmatrix}
+ 2
\begin{bmatrix}
    3 \\ 0
\end{bmatrix}
$$


$$
\overrightarrow{V} = 
\begin{bmatrix}
    -1(1) + 2(3) \\
    -1(-2) + 2(0)
\end{bmatrix}= 
\begin{bmatrix}
    5 \\
    2
\end{bmatrix}
$$

This means that a 2d transformation can be described by just 4 numbers. This can now be represented as a **2x2 Matrix**. ${\mathbf{\hat{\imath}}}$ and ${\mathbf{\hat{\jmath}}}$ are columns 1 and 2 respectively. 


$$
\begin{bmatrix}
    3 & 2 \\
    -2 & 1
\end{bmatrix}
*
\begin{bmatrix}
    -1 \\
    2
\end{bmatrix}
= 
\begin{bmatrix}
    5 \\
    2
\end{bmatrix}
$$

This is matrix multiplication! Note that matrix can also be "Shear" where ${\mathbf{\hat{\imath}}}$ and ${\mathbf{\hat{\jmath}}}$ are not at right angles. Remember that if the basis vectors are **Linearly dependent** then all of their span is reduced to a single line.

To sum up given that ${\mathbf{\hat{\imath}}}$ = a, b and ${\mathbf{\hat{\jmath}}}$ = c, d:

$$
\begin{bmatrix}
    a & b \\
    c & d
\end{bmatrix}
\begin{bmatrix}
    x \\
    y
\end{bmatrix}
= x 
\begin{bmatrix}
    a \\
    c
\end{bmatrix}
+y
\begin{bmatrix}
    b \\
    d
\end{bmatrix}
=
\begin{bmatrix}
    ax+by \\
    cx+dy
\end{bmatrix}
$$

