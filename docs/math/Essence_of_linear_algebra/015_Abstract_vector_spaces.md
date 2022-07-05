# Abstract vector spaces

<iframe width="560" height="315" src="https://www.youtube.com/embed/TgKwz5Ikpc8" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

What are vectors? Are they arrows? Are they pairs of numbers? Or is it something deper? 

If they are not simply lists of numbers, what do mathematicians mean when they use the term "spacial". Let's talk about other *vectorish* things: functions. Really, functions are just another type of vector. 

Let's talk about adding functions. $(f + g)$ as an example. The output of the product of f and g is a new function, that at any given input adds f and g together. $f(-4.00) + g(-4.00)$ or $f(x) + g(x)$. This is similar to adding vectors coordinate by coordinate, except there are infinitly many coordinates to deal with. It's similar to how to think of scaling functions. Which, once again, is similar to scaling a vector coordinate by coordinate.  

This goes to figure that we could apply many of the concepts that work on vectors, like linear transformations, null space, dot product, eigen-everything to functions in a similar way. For example you can linearly transform a function, that transformation would take in one function and return another. 

$$
L
\Bigg(
\frac{
    1
}{
    9
}
x^3 - x
\Bigg)
=
\frac{
    1
}{
    3
}
x^2 -1
$$

One familar example comes from calculus: the derivative. It transforms one function into another function. When transforming functions instead of vectors the term used is **linear operatros** insteead of transforms. But what does it mean for a transformation of functions to be linear? The formal definition:

* Additivity: $L( \overrightarrow{v} + \overrightarrow{w} ) = L( \overrightarrow{v} ) + L ( \overrightarrow{w} )$
* Scaling: $L(c \overrightarrow{v})= c L ( \overrightarrow{v} )$

A transformation is linear if it satisfies the above two properties. Additivity means that if you add two vectors and get a sum, then apply a transformation, you get the same result as if you added the transformed versions of the two vectors. Likewise, the scaling property says that if you scale two vectors, then apply a transformation, you get the same result as scaling two vectors by the same amount after the transformation. 

Linear transformations **preserve** addition and scalar multiplication. 

Grid lines remaining parallel and evenly spaced is a visual representation of those two properties. 

The order of operation does not matter. You can trasnform and add, or add and transform and the result is the same. A good comparison can be made to the derivative function in calculus. The derivative is linear, but since I have no idea what that is the comparision doesn't really work for me (future study!). But the point is that they are both from the same family, and that you can apply linear trasnformations (or operations) to both of them. Many of the concepts of linear algebra have direct correlation to functions. 

| Linear algebra concepts | Alternat names when applied to functions |
| -- | -- |
| Linear Transformations | Linear operators |
| Dot products | Inner products |
| Eigenvectors | Eigenfunctions | 

So how does this help us know what a vector is? The point is that there are many *vectorish* concepts in math that can have transformations applied.  All of these vectorish things are called *vector spaces*. Vectors are one subset of these spaces, that have a discreet list of rules. They are the **axioms** of vectors. There are 8 axioms for vector space:

1. $\overrightarrow{u} + ( \overrightarrow{v} + \overrightarrow{w} ) = ( \overrightarrow{u} + \overrightarrow{w} ) + w$
2. $\overrightarrow{v} + \overrightarrow{w} = \overrightarrow{w} + \overrightarrow{v}$
3. there is a vector 0 such that $0 + \overrightarrow{v} = \overrightarrow{v} \text{for all} \overrightarrow{v}$
4. For every vector $\overrightarrow{v}$, there is a vector $- \overrightarrow{v}$ so that $\overrightarrow{v} + (- \overrightarrow{v}) = 0$
5. $a(b \overrightarrow{v})=(ab) \overrightarrow{v}$
6. $1 \overrightarrow{v} = \overrightarrow{v}$
7. $a( \overrightarrow{v} + \overrightarrow{w} ) = a \overrightarrow{v} + a \overrightarrow{w})$
8. $(a + b) \overrightarrow{v} = a \overrightarrow{v} + b \overrightarrow{v}$dfa

These axioms are not so much rules of nature as an interface between us and other people. They are a list of things that guarantee that linear algebra will work with a given *vector space*. This is why most textbooks talk about vectors in terms of additivity and scalability. The form that vectors take doesn't really matter as long as it follows these rules. 



