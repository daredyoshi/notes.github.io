<iframe width="560" height="315" src="https://www.youtube.com/embed/etdSXlVjXss" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


We need to start by building the minimal groundwork we need first so that we can then start iterating. Need to focus on what's achievable by us. 

Basic overview of what the engine will need in a bare bones way:

* Entry Point

Launch the application and what happens? What gets executed with the main function? What controls what happens?

* Application Layer

A section of our ode that deals with application lifestyle and events. What keeps time going forward? Timeline? Intput events?

* Window layer

Technically this only exists on desktop platforms. It's different than mobile platforms where the "app" handles everything. This deals with input & events. This also handles recirving and sending events. It should also allow querying the state of an intput device. 

* Renderer 

This is obviously a huge part of game engine development.

* Render API abstraction

For me this will probably be and stay opengl, but it would be worth abstracting for a potential vulkan implementation. By far this is one of the most interesting and complicated pars of a game engine. 

* Debugging support

What does it mean to add debugging support to an application? We need good ways to figure out what's going on. Something like a logging system. Get a profiling system, so you can get logistics on any platform. Something that times every function. 

* Scripting 

Basic scripting language build in. That way we can write scripts and make things easy for artists

* Memory Systems

Memory is a huge deal with performance, and it's something that we want to be able to inspect. 

* Entitiy-component System (ECS)

A way to include components and systems with game entity objects. Objects, Actors, Constraints etc. How to attach entities to each other. 

* Pysics Solution 

At first I balked at this, but it would be really useful to have ragdolls. Not a first level priority, but something to add ad some point. 

* Build System

Pre-optimize scenes/background caching something simlar. There are many things vfx can learn from game engine techniques. Reload assets in realtime. Collaborative editing? How do things interact with the filesystem?

Next up... get started!
