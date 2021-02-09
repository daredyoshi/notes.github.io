# Vectors

The definition of a vector and how to think about it [Abstract vector spaces](../Essence_of_linear_algebra/015_Abstract_vector_spaces)


## Basis vectors ${\mathbf{\hat{\imath}}}$ and ${\mathbf{\hat{\jmath}}}$ 
These are the coordinate vectors by which a vector may be multiplied to be transformed into their coordinate space. 

The **span** of two vectors is the entirety of possible vectors in a coordinate space. 

[Linear combinations, span and basis vectors](../Essence_of_linear_algebra/002_Linear_combinations_span_and_basis_vectors))

## Transforming a vector by a matrix
This goes over exactly how it happens that a vector is transformed by a matrix (think of matrix as a function) [Linear Transformations and Matrices](../Essence_of_linear_algebra/003_Linear_transformations_and_matrices)

## Dot product
The dot project projects two vectors onto the number line. If it's positive they are pointing similar directions, 0 if they are perpendicular and negative if they are pointing away from each other. It's the same as making a 1 row matrix out of the first vector and treating it as a transform. Read more [here](../Essence_of_linear_algebra/009_Dot_products_and_duality)

A utilitarian breakdown of the dot product can be found [ here ](../math/Math_for_Game_Developers/008_Backstabbing_Dot_Product.md)

## Cross Product 
Aka "vector product" - this is like the dot product but for each row of the vector without the linear transformation. Read the [basics](../Essence_of_linear_algebra/010_Cross_Products) and then why the basis vectors are used in the first columns [here](../Essence_of_linear_algebra/011_Cross_Products_in_the_light_of_linear_transformations)

## Rotating a vector in a different coordiante space
What if you want to rotate a joint not in it's parent coordinate space but in that of another joint in a different hierarchy? [Change of basis](../Essence_of_linear_algebra/013_Change_of_basis)
