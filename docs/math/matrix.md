# Matrix Fundamentals

## Basic theory and multiplication 
You can read about how to transform a vector by a matrix in [Linar Transformations and Matrices](../Essence_of_linear_algebra/003_Linear_transformations_and_matrices)

This goes over th exact process of multiplying matricies [Matrix multiplication as composition](../Essence_of_linear_algebra/004_Matrix_multiplication_as_composition)

Multiplying 3d vectors written out [Three-dimensional linear transformations](../Essence_of_linear_algebra/005_Three-dimensional_linear_transformations)

The determinant of the matrix is the volume/area that it's basis vectors take up. Read more on how this works [here](../Essence_of_linear_algebra/006_The_determinant)

Taking the dot product of two vectors is the same operation as creating a one row matrix out of the first one and treating it as a transform. [link](../Essence_of_linear_algebra/009_Dot_products_and_duality)


## Rotating a vector in a different coordiante space
What if you want to rotate a joint not in it's parent coordinate space but in that of another joint in a different hierarchy? [Change of basis](../Essence_of_linear_algebra/013_Change_of_basis)


Cramer's rule is not really useful other than a conceptual thought experiement, because you could also just use gaussian elemination with an inverted matrix to get the same resule with better computation efficiancy. It's useful for determining single axes of vectors though, and to prove things geometrically. [Cramer's rule, explained geometrically](../Essence_of_linear_algebra/012_Cramers_rule_explained_geometrically)
