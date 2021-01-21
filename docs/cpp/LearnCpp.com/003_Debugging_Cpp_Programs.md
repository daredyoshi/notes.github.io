# Debugging C++ Programs

## Syntax and semantic errors

A **syntax error** occurs when you write a statement that is not valid according to the grammar of C++. It's often missing semicolons, undeclared variables, mismatched braces etc. A **semantic error** occurs when the statement is syntactically valid, but does not do what you want it to. 

To find semantic errors you will need to debug your problem. There are generally five steps:

1. Find the root cause of the problem
2. Ensure you understand why the issue is occurring. 
3. Determin how you'll fix the problem
4. Fix the problem
5. Retest everything around the problem and write tests so it doesn't happen again

The hardest part of all this is usualylly the first step. **Reproduction steps** are a series of steps needed to get the problem to repeat. In houdini I would create an empty scene with none of the garbage, and try to reproduce the problem without what the artist is doing and then one-by-one add the element the artist had in the scene until the problem comes. These steps are the reproduction steps. 

To find a problem in complex code if you have absoutely no idea where it is, you play the game of high-low. I did this when the vex compiler was broken and not giving me the correct errors. Just comment out half the code, see if it works, etc. 

Some common simple debugging tactics:

1. Commenting out code
2. Print function executions ( use std::cerr for this no std:cout!! )
3. Printing values

These aren't very optimal though, because you are modifying your code and will have to undo all these changes again after you have found the problem which can lead to more errors!

One thing you could consider for option 2 is to add preprocessing directives to clear all debug statements if a variable like ENABLE_DEBUG is not defined. This still makes your code harder to read than it should be, so I would preferr using a logger. 

A logger writes a **log file** to disk that records events that occur in software. This process is called **logging**. These don't clutter up your code, and you can easily get them from users of your software without asking them to copy their terminal output. 

I was very confused about how to add plog since the tutorial said I would need to add it to my IDE paths, but on linux building plog from AUR it automajikally just puts it into /usr/include which the linker knows about.

## Using a debugger

I'll be learning using the gdb debugger. Vim 8.1 has a beautiful gdb integration. To start it run

```
:packadd termdebug
:Termdebug
```

Note that you need to run the debug version of the build as the release version won't work!

It's a visual debugger that allows stepping through code. You can set breakpoints visually in vim using ```:Break``` and ```:Clear``` to remove them. There's a tutorial on how to use it [here](https://www.dannyadam.com/blog/2019/05/debugging-in-vim/)

At any point you can issue commands like ```print(var)``` to help figure out what is going on. 

* **Step** executes the next statment in the normal execute path of the program and the npauses the execution of the program. It goes into all functions. It's equivalent to **step into** for visual studio.
* **Next** goes to the next line and pauses there. It's equivalent to **step over** in visual studio.
* **Finish** I *think* is like **step out** wher it finishes the current function and then gives you control again. 
* **Continue** runs the program until the next breakpoint. 
* There's not call stack in gdb but you can always call **backtrace** to see what has ben run so far. 




