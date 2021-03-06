# Linear combinations, span, and basis vectors

<iframe width="560" height="315" src="https://www.youtube.com/embed/k7RM-ot2NWY" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Basis Vectors $\mathbf{\hat{\imath}}$ and ${\mathbf{\hat{\jmath}}}$   
There are two vry special vectors in the x, y coordinate system. ${\mathbf{\hat{\imath}}}$   is the unit vector of the x axis and ${\hat{\mathbf{\jmath}}}$  is the unit vector of the y axis. Unit vector means they both have a magnitude of 1. 

When given a vector you can imagine that vector scaling ${\mathbf{\hat{\imath}}}$   and ${\mathbf{\hat{\jmath}}}$   

$$
\begin{bmatrix}
    a \\
    c \\
\end{bmatrix}
=(a){\hat{\imath}}
* (c){\hat{\mathbf{\jmath}}}
$$

This multiplies the unit vectors so that when you add them together you arrive at the input vector. If we choose different basis vectors we basically use a different coordinate system. By modifying the basis vectors you can translated into any space. Describing vectors numerically depends entirely on the basis vectors (CG "World space")

The **"span"** of $\overrightarrow{{\hat{\mathbf{\imath}}}}$ and $\overrightarrow{{\hat{\mathbf{\jmath}}}}$ is the set of all their **linear combinations.** No matter how much $\overrightarrow{{\hat{\mathbf{\imath}}}}$ and $\overrightarrow{{\hat{\mathbf{\jmath}}}}$ have been transformed. After transformation they would no longer be ($\overrightarrow{{\hat{\mathbf{\imath}}}}$ and $\overrightarrow{{\hat{\mathbf{\jmath}}}}$ ) but something else, in our case $\overrightarrow{v}$ and $\overrightarrow{w}$.

$$
a\overrightarrow{v}+b\overrightarrow{w}
$$

Let *a* and *b* vary over all real numbers. 

### If both basis vectors have the same direction
Then any input vector can only scale in that direction.In that case the **span** of the vectors is 1 dimensional along a single line. 

Vectors are commonly represented as points. 

### Span in 3d
What does it mean to take 2 vectors' span in 3d space? 
Just like with 2d vectors you scale them and then add them all together.

$$
a\overrightarrow{v}+b\overrightarrow{w}+c\overrightarrow{u}
$$

If the third vector is in the same direction as one of the first two, you are stuck in the 2d space of the first two vectors like a flat sheet. When a vector like this is redundant it is called **Linearly dependent**, since it is already in the span. If each vector is adding something to the span the vectors are considered **Linearly Independant**.

**Basis** Vectors are always **Linearly Independant**










