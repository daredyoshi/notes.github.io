# Cross Products
<iframe width="560" height="315" src="https://www.youtube.com/embed/eu6i7WJeinw" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

*"Every dimension is special."* -Jeff Lagarias

*"In Russian math school we call dot product a "scalar product" and a cross product a "vector product". And I think this is very intuitive naming"* -mchlkzmn youtube comment

The Cross product is the area of the parallelogram created by two vectors. The parallelogram is created by adding each vector to the other vector. It's written like this

$$
\overrightarrow{v} 
\times
\overrightarrow{w}
=
\text{Area of parallelogram}
$$

It also depends on the order of teh vectors. If $\overrightarrow{v}$ is on the right of $\overrightarrow{w}$ then the cross product is positive, on the left it is negative. Order matters! You can remember the order based on the order of a vector (x, y, z) - if the vectors are in this order the result is positive. 

$$
\overrightarrow{x} 
\times
\overrightarrow{y}
= 
\text{positive}
$$

$$
\overrightarrow{y}
\times
\overrightarrow{x}
= 
\text{negative}
$$

To calculate the cross product we use a very similar method to calculating the [determinant](../006_The_determinant). 

$$
\overrightarrow{v}
\begin{bmatrix}
    3 \\
    1
\end{bmatrix}
\times
\overrightarrow{w}
\begin{bmatrix}
    2 \\
    -1
\end{bmatrix}
=
det
\Bigg(
\begin{bmatrix}
   3 & 2  \\
   1 & -1 
\end{bmatrix}
\Bigg)
$$

The determinant is all about measuring how areas change due to a transformation. You create the transformation by "transforming" to the vectors that are being crossed. If $\overrightarrow{v}$ is on the left of $\overrightarrow{w}$, then it means that orientation was flipped, and you get the negative result. 

In 3d this becomes a lot more interesting. This area will become the **length** of the new vector. The direction of that new vector is going to be perpendicular to the two input vectors. Note that this vector always points to the right of the plane of x and y on your right hand (**right hand rule**). The thumb is the cross product. 

The actual method for determining the cross product is strange. 
It's like calculating the determinant, but only for each row which then becomes the new vector. 

$$
    \begin{bmatrix}
        v_1 \\
        v_2 \\
        v_3
    \end{bmatrix}
\times
    \begin{bmatrix}
        w_1 \\
        w_2 \\
        w_3
    \end{bmatrix}
= 
det
\Bigg(
\begin{bmatrix}
    {\mathbf{\hat{\imath}}} & v_1 & w_1 \\
    {\mathbf{\hat{\jmath}}} & v_2 & w_2 \\
    {\mathbf{\hat{k}}} & v_3 & w_3
\end{bmatrix}
\Bigg)
$$

$$
{\mathbf{\hat{\imath}}} 
(
v_2 w_3 - v_3 w_2) +
{\mathbf{\hat{\jmath}}} 
( v_3 w_1 - v_1 w_3 ) + 
{\mathbf{\hat{k}}}
(v_1 w_2 - v_2 w_1)
$$

Why on earch are the basis vectors there?
We go over this in the next lesson!


