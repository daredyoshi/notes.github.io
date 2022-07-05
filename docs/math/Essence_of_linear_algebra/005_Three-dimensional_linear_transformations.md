# Three-dimensional linear transformations

<iframe width="560" height="315" src="https://www.youtube.com/embed/rHLEWRxRGiM" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
 
Most of these examples will be in 2 dimensions, but occasionally it's goot to peer out of flatland to see how things apply in higher demensions. 

In the third dimension we have one addition basis vector: ${\mathbf{\hat{k}}}$, which represents the **z axis**.

The three basis vectors form the columns of a 3x3 matrix, commonly referred to as a **rotation matrix**. 

To see where a three dimensional vector lands, the math is almost identical to a 2d vector:

$$
\overrightarrow{v}
=
\begin{bmatrix}
    x \\
    y \\
    z
\end{bmatrix}
=
x {\mathbf{\hat{\imath}}} + y {\mathbf{\hat{\jmath}}} + z {\mathbf{\hat{k}}}
$$

The important part is that this scaling and adding works before and after the transformations. Let's try to write out the multiplication like the 2d matrix from the previous section.

$$
\overbrace{
    \textcolor{magenta}{
        \begin{bmatrix}
            4 & 7 & 8 \\
            3 & 0 & 9 \\
            2 & 1 & 6 \\
        \end{bmatrix}
    }
}^{M_1}
\overbrace{
    \begin{bmatrix}
        \textcolor{red}{
            9
        } & \textcolor{green}{
            3
        } & \textcolor{blue}{
            5
        } \\
        \textcolor{red}{
            0
        } & \textcolor{green}{
            1
        } & \textcolor{blue}{
            2
        } \\
        \textcolor{red}{
            3
        } & \textcolor{green}{
            2
        } & \textcolor{blue}{
            8
        }
    \end{bmatrix}        
}^{M_2}
=
\begin{bmatrix}
    36 + 0 + 18 & 12 + 7 + 16 & 20 + 14 + 36 \\
    27 + 0 + 18 & 9 + 0 + 18 & 15 + 0 + 72 \\
    18 + 0 + 18 & 6 + 2 + 4 & 10 + 2 + 48
\end{bmatrix}
=
\begin{bmatrix}
    54 & 35 & 70 \\
    46 & 27 & 87 \\
    36 & 12 & 60
\end{bmatrix}
$$

$$
\textcolor{magenta}{
    \begin{bmatrix}
        4 & 7 & 8 \\
        3  &0 & 9 \\
        2 & 1 & 6
    \end{bmatrix}
}
\textcolor{red}{
    \begin{bmatrix}
        9 \\
        0 \\
        3
    \end{bmatrix}
}
=
\textcolor{red}{
    9
}
\textcolor{magenta}{
    \begin{bmatrix}
        4 \\
        3 \\
        2
    \end{bmatrix}
}
+ \textcolor{red}{
    0
}
\textcolor{magenta}{
    \begin{bmatrix}
        7 \\
        0 \\
        2
    \end{bmatrix}
}
+ \textcolor{red}{
    3
}
\textcolor{magenta}{
    \begin{bmatrix}
        8 \\
        9 \\
        6
    \end{bmatrix}
}
=
\begin{bmatrix}
    54 \\
    46 \\
    36
\end{bmatrix}
$$


$$
\textcolor{magenta}{
    \begin{bmatrix}
        4 & 7 & 8 \\
        3  &0 & 9 \\
        2 & 1 & 6
    \end{bmatrix}
}
\textcolor{green}{
    \begin{bmatrix}
        3 \\
        1 \\
        2
    \end{bmatrix}
}
=
\textcolor{green}{
    3
}
\textcolor{magenta}{
    \begin{bmatrix}
        4 \\
        3 \\
        2
    \end{bmatrix}
}
+ \textcolor{green}{
    1
}
\textcolor{magenta}{
    \begin{bmatrix}
        7 \\
        0 \\
        2
    \end{bmatrix}
}
+
\textcolor{green}{
    2
}
\textcolor{magenta}{
    \begin{bmatrix}
        8 \\
        9 \\
        6
    \end{bmatrix}
}
=
\begin{bmatrix}
    35 \\
    27 \\
    12
\end{bmatrix}
$$

$$
\textcolor{magenta}{
    \begin{bmatrix}
        4 & 7 & 8 \\
        3  &0 & 9 \\
        2 & 1 & 6
    \end{bmatrix}
}
\textcolor{blue}{
    \begin{bmatrix}
        5 \\
        2 \\
        8
    \end{bmatrix}
}
=
\textcolor{blue}{
    5
}
\textcolor{magenta}{
    \begin{bmatrix}
        4 \\
        3 \\
        2
    \end{bmatrix}
} + 
\textcolor{blue}{
    2
}
\textcolor{magenta}{
    \begin{bmatrix}
        7 \\
        0 \\
        2
    \end{bmatrix}   
}
+ \textcolor{blue}{
    8
}
\textcolor{magenta}{
    \begin{bmatrix}
        8 \\
        9 \\
        6
    \end{bmatrix}
}
= 
\begin{bmatrix}
    70 \\
    87 \\
    60
\end{bmatrix}
$$

And all of this can be summarized as:

$$
\begin{bmatrix}
    a & b & c \\
    d & e & f \\
    g & h & i
\end{bmatrix}
\begin{bmatrix}
    j & k & l \\
    m & n & o \\
    p & q & r
\end{bmatrix}
=
\begin{bmatrix}
    aj + bm + cp & ak + bn + cq & al + ao + ar \\
    dj + em + fp & dk + en + fq & dl + eo + fr \\
    gj + hm + ip & gk + hn + iq & gl + ho + if
\end{bmatrix}
$$


