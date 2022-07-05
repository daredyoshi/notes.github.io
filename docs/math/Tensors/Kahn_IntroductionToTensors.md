# Introduction to Tensors

<iframe width="560" height="315" src="https://www.youtube.com/embed/uaQeXi4E7gA" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

If you are calculating the direction and magnitude between two points (i.e. NY Harbor and the Empire State building) you have a vector which a magnitude. So basically where $\overrightarrow{d}$ is the displacement -19 is the displacement in x, 12 in j and 0.45 in k:

$$
\overrightarrow{d} = -19 { \hat{\mathbf{\imath}}  }+ 12 \hat{\mathbf{\jmath}} + 0.45 \hat{\mathbf{k}}
$$

These three vectors that give the displacement are the component vectors of the displacement. Each component to the displacement is a vector with a single dimension. 1 Basis vector for each component.

### Expressing force with tensors

If we want to know the forces acting on a specific point in a construction beam, we can slice the beam by three axis planes. For each of these planes we can then calculate a force which is expressed as such $P_{xx}$, $P_{ xy }$ and $P_{xz}$, where x is the plane that the force is perpendicular to. We can combine these all into a three by three matrix. 

$$
P = 
\begin{bmatrix}
    P_{xx} & P_{xy} & P_{xz} \\
    P_{yx} & P_{yy} & P_{yz} \\
    P_{zx} & P_{zy} & P_{zz}
\end{bmatrix}
$$

*Why can't we just add all of these together into a single vector?*

$P_{xx} + P_{xy} + P_{xz}$

Even though the forces are al acting in the same direction, the nature of the forces is differnt. We need the whole matrix because $P_{xx}$ might be pulling, but $P_{xy}$ might be exerting a shearing force. These different forces effect the beam in different ways. That's why it's important to specify the surface that the force acts on as well as the force itself. thus, to specify stresses, use P, which has 9 components (2 basic vectors /component: 1 for cross-sectional area + 1 for direction of force). We need to know the direction of the surface area and the direction of the force.

This matrix is considered a tensor: 

!!! info
    In an m-dimesional space, a tensor of rank n is a mathemathical object that has n indices, $m^n$ components, and obeys certain transformation rules.

    Generally, m= 3

!!! info
    The rank of the tensor is the amount of components per basis vector. A 3d vector has a rank of 1. The P tensor from the board has a rank of 2 aka. a stress tensor.
    
There is a fairly common misconception that tensors and matrices are the same thing. Even if their data is serialized to the same types, it turns out that tensors obey *special transformation rules* that matricies don't. You can use a matrix to represent a tensor, but they behave differently. We can also use 3d arrays to represent rank 3 tensors. Each component of the tensor is specified by 3 basisvectors and there are $3^3 =27$ total components.

The next video goes over the special *transformation rules*. 
