# The Essence of Caculus

<iframe width="560" height="315" src="https://www.youtube.com/embed/WUvTyaaNkzM" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

This series is designed to illustrate some of the core concepts of calculus in a way that feels like discovering it yourself. 

We will start with the area of a circle: $\pi R^2$. Why is this the case? Pondering this will lead to three of the big ideas in calculus: **integrals**, **derivatives**, and the *fact that they're opposites. 

Start approaching the circle problem by taking a layer of your circle like an onion, and figuring out the circumference of that: $2\pi r$. The thickness of this layer is aribitrary, but determines the area of the layer. Sticking to standard calculus notation, let's label that thickness: $dr$ "difference in radius". So now the area of this thin ring (approximated to a rectangle) is $2\pi r d r$. This approximation gets better and better the thinner the layer slice is. Now let's imagine we do this to every single layer of the circle, from 0 to the full radius (using a full radius of 3 as the example). The spacing of these r values corresponds to the thickness of each ring, again "difference in radius" $d r$. You can imagine each of these layers stacked next to each other "unwrapped" flat on top of the number line ever increasing as r increases.Each of the layers is $2\pi r$, the circumference of the cooresponding ring that each rectangle approximates. 

This can be visualized as a graph where each of the rectangles extends upwards to where it just barely touches the line of $2 \pi r$ - which is not linear. 

<iframe src="https://www.desmos.com/calculator/lqina0ttgj?embed" width="500px" height="500px" style="border: 1px solid #ccc" frameborder=0></iframe>

The smaller you make each of the rectangles, the closer they are to the above line. Since this creates a triangles you can get the area of it with:

$$
\text{Area} = 
\frac{
    1
}{
    2
}
b h
$$

$$
= \frac{
    1
}{
    2
}
(r)(2 \pi  r)
$$

$$
\pi r^2
$$

Now if you are thinking of a mathematician, you are not just thinking about finding the answer, but about using the process to generalize problem solving tools and techniques. The way we transitioned from the approximation to the precise result was by going an infinitude smaller. In this case that variables that kept getting smaller was the "difference in radius" $d r$. The sum of the area of the rectancles, with more and more samples, approaches the area of the graph. With these results we can then simplify the problem to being the area under the graph instead of the aggregate sum of all the samples. 

A lot of other hard problems in math can also be broken down as the "Sum of many small values". Like figuring out how far a car has traveled based on it's velicity at each point in time $v(t)dt$. It's the science of interpolating curves.  

Let's take the example of the x parabola. How would we find the area at any given value of x?

<iframe src="https://www.desmos.com/calculator/ijhkps2liu?embed" width="500px" height="500px" style="border: 1px solid #ccc" frameborder=0></iframe>

This fucntion to get the area at any value of x is called the **Integral** of x^2. $A(x)$. This right now is just a mystery to us. Finding these integral fucntions is genuinely hard. Just like with the circle, let's slice this function up into slivers, with each sliver width being the "difference in X" $dx$ and each sliver area being "difference in Area", $dA$. That sliver can be pretty well approximated by a rectancle who's height is $x^2$ and width is $dx$. This allows us to say that the difference in area is approximate to $dA \approx x^2 dx$

$$
\frac{
    dA
}{
    dx
}
\approx x^2
$$

Let's take the points 3.001 and 3 with a d of 0.001 as an example:

$$
\frac{
    A(3.001) = A(3)
}{
    0.001
}
\approx 3^2
$$

This provides a very strong clue that we can work with. And the solution is not unique to the graph of $x^2$. It applies to any graph. This lets us stuble into another big idea of calculus: **derivatives**. This "difference in Area" of the function is called the "Derivative of A". But technically, the derivative is whatever the formula approaches as d gets closer and closer to zero. Esseneially the derivative is a measure of how sensitive a function is to small changes in its input. This should give a high level overview of what calculus is about. 







