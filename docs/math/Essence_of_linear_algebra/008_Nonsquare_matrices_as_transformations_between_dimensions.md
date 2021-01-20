# Nonsquare matrices as transformations between dimensions

How do you transform matrices that do not have the same number of rows as columns? It's perfectly reasonable to talk about transoforms between dimensions. 

$$
\overbrace{
    \begin{bmatrix}
        2 \\
        7
    \end{bmatrix}
}^{\text{2d input}}
\to
L( \overrightarrow{v} )
\to
\overbrace{
    \begin{bmatrix}
        1 \\
        8 \\
        2
    \end{bmatrix}
}^{\text{3d output}}
$$

You can encode 2d information in 3d by just having 2 columns. The span of this matrix will be a plane. Similarly a three column two row matrix has three axees each on two dimensions. The span of this is also a plane. Basically a flattened set of vectors. Squishing vectors to a single line has ties to the dot product



