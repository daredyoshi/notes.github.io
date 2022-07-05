# Math for Game Developers - Jumping and Gravity (Time Delta, Game Loop)

<iframe width="560" height="315" src="https://www.youtube.com/embed/c4b9lCfSDQM" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

So we know that mario likes jumping. But how do we create the curved path that mario's jump trajectory makes? We do this by cutting up the curve into a lot of tiny pieces. Each piece moves mario a little bit along the jump vector $\overrightarrow{v}$ and the gravity vector $\overrightarrow{g}$. Every one of these pieces is one iteration of the **Game Loop**. The game loop is as follows:

1. Update (things)
2. Draw (stuff)
3. Loop

So back to the jump - how long should each of these cuts be? To answer that we need $\Delta T$. This is the time that passes between each frame. Each game loop is one frame. $\Delta T$ is the time that passes between each frame. 

$$
\Delta T = t^{\prime} - t
$$

So if we're running at 30 fps, then $\Delta T$ is 1/30 = 0.33333. At sixty this is even less. So bascially these are very tiny pieces. So how do we update mario's position (m) each frame to craete this arc?

$$
m^{\prime} = m + \Delta t \overrightarrow{v}
$$

Now in the same loop we need to update the velocity vector

$$
\overrightarrow{v}^{\prime} = \overrightarrow{v} + \Delta t \overrightarrow{G}
$$

The code from here on out includes files from Jorge's own games, so it's hard to reproduce. I'll revisit this series after going through opengl and creating a basic game with a game loop. 


