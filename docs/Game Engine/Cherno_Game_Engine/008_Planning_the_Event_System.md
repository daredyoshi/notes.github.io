# Planning the Event System

This is another planning session. 

<iframe width="560" height="315" src="https://www.youtube.com/embed/5mlziHwq90k" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Here we think about how we will deal with window events that we might receive. This is being done before the window system is added. 

Right now we have an **Application** Class. 

This needs to be able to receive events and dispatch them to something we're going to call "layers". More on that in a future vid. Just know that this is what evens will be propagated to. 

So we also have a **Window** class. This is a representation of our actual application window. Stuff comes in through the window class. But we don't want to tie our application to our window class. The Application creates and maintains the window class, but this should be through an api layer. So our window class will receive an event, and this then needs to get translated into a Choreo event. 

So we need an **Event** class which has all the details about an event that has occurred. And we need Application to provide window a callback that it can tie to these events. This could be just a function pointer, or something more abstract. This is so that ever time the window gets and event, it can call the callback. Window gets notified about which function to use, which will be called from within application. Through all of this window never stores a direct reference to application.  

Then we can expand to something like a buffered event system etc etc. But that's something to explore in the future. 

So we need classes for all the events, event dispatcher, listener etc. 






