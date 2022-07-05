# Cross products in the light of linear transformations

<iframe width="560" height="315" src="https://www.youtube.com/embed/BaM7OCEm3G0" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Why are the symbols of the first column ${\mathbf{\hat{\imath}}}$ ${\mathbf{\hat{\jmath}}}$ and ${\mathbf{\hat{k}}}$?


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

This results in three numbers that are then used in a vector... but whyyy. 
This goes back to te mathemathical concept of duality covered [here](../009_Dot_products_and_duality). When linearly transforming vectors to the numberline, you will be able to match those vectors to **dual vectors** of those transformations. So that performing the linear transformation is the same as taking a dot product with that vector. 

The cross product is an example of this process in action. We're going to walk through this:

1. Define a 3d-to-1d linear transformation in germs of $\overrightarrow{v}$ and $\overrightarrow{w}$. 

2. Find its **dual vector**

3. Show that **dual vector** == **cross product**

Remember that for 2d, the cross product is an ordinary determinant returning a 1d number, which is the area of that parallelogram spanned out by those two vectors which is negative or positive depending on if the vectors are flipped in relation to their basis vectors. 

Also remember that the determinant of a 3x3 transform gives the volume of that parralelopiped created by those three vectors. This is different than the cross product which takes in three vectors and spits out a vector instead of a scalar. 

Let's start by defining a function to get the volume of a parralelopiped with vectors $\overrightarrow{v}$ and $\overrightarrow{w}$ given arbitrary input vector made up of x, y, z. 

$$
\large f
\Bigg(
\begin{bmatrix}
    x \\
    y \\
    z
\end{bmatrix}
\Bigg)
=
det
\Bigg(
\begin{bmatrix}
    x & v_1 & w_1 \\
    y & v_2 & w_2 \\
    x & v_3 & w_3
\end{bmatrix}
\Bigg)
$$

An important part to remember is that this function is linear because it's outputting a scalar value. This means we can bring in the idea of a dual vector. It means there is some way to describe this function as matrix multiplications. For any fucntion that goes from three dimensions to one dimensions there will be a 1x3 matrix describing that transformations. Remember that this process is the same as the dot product, so that is what we will use to spell out the formula. 

In this formula we want to solve for $\overrightarrow{p}$.

$$
\overbrace{
    \begin{bmatrix}
        p_1 \\
        p_2 \\
        p_3
    \end{bmatrix}
}^{ \overrightarrow{p} }
\cdot
\begin{bmatrix}
    x \\
    y \\
    z
\end{bmatrix}
=
det
\Bigg(
\begin{bmatrix}
    x & v_1 & w_1 \\
    y & v_2 & w_2 \\
    x & v_3 & w_3
\end{bmatrix}
\Bigg)
$$

$$
p_1 * x + p_2 * y + p_3 * z = x(v_2 * w_3 - v_3* w_2) + y(v_3 * w_1 - v_1 * w_3) + z(v_1 * w_2 - v_2 * w_1)
$$

$$
p_1 = v_2*w_3-v_3*w_2
$$

$$
p_2 = v_3*w_1-v_1*w_3
$$

$$
p_3 = v_1*w_2-v_2*w_1
$$

This is very similar to what we do when we manualy calculate a cross product like in the previous section. Only that ${\mathbf{\hat{\imath}}},$ ${\mathbf{\hat{\jmath}}},$ and ${\mathbf{\hat{k}}}$ is a way of signalling that we should interpret those coefficients as the coordinates of a vector. What all of this is saying is that the computation for the cross product can be considered a way to anser the question: 

*What vector $\overrightarrow{p}$ has the property that when you take a dot product between $\overrightarrow{p}$ and some $\overrightarrow{xyz}$, it gives the same result as plugging in $\overrightarrow{xyz}$ to the first column of a matrix whose other two columns have the coordinates of $\overrightarrow{v}$ and $\overrightarrow{w},$ and then computing the determinant.*

Let's rephrase this question in geometrical terms:

*What vector $\overrightarrow{p}$ will return the area of a parralelopiped when you take it's dot product with another $\overrightarrow{xyz}$, that returns the same volume as when you take the determinant of a matrix with columns $\overrightarrow{xyz}$ and $\overrightarrow{v}$ and $\overrightarrow{w}$.* 

$$
(
\text{Area of parallelogram}
)
* 
(
\text{Component of }
\overrightarrow{xyz}
\text{ perpendicular to }
\overrightarrow{v}
\text{ and }
\overrightarrow{w}
)
$$

This is the same of taking the dot product of *any vector* $\overrightarrow{xyz}$ and $\overrightarrow{v}$ and $\overrightarrow{w}$ then setting that to the length of the parallelogram. If the vector is normalized (like the basis vector) you simply need to multiply it by the determinant of $\overrightarrow{v}$ and $\overrightarrow{w}$. If, like the basis vectors, that vector is in the right order, the right hand rule will multiply it by -1 if the vectors are in the other direction. 

This is why $\overrightarrow{xyz}$ must be the basis vectors. 



