# The Mikado Method

## What is it?

The Mikado method borrows its name from the game commonly known as pick-up sticks.

In the game, a bunch of sticks are jumbled and dropped on the floor. Players take turns removing sticks but must do so without either moving or touching the other ones. If they do, the sticks need to be jumbled and dropped again. This can happen 3 times before the game is over.

The goal of the game is to be the player with the highest score. For each stick you pick-up successfully you get points. Points are associated with the color of the stick. The more valuable a stick is, the less chance you have of finding it in the bunch. For instance, the highest value stick, known as the Mikado, only occurs once and is worth 20 points. The red stick on the other hand is worth 2 points and can be found 15 times within the bundle.

![Mikado, the game](/assets/images/mikado-method/mikado-game.jpeg)
{: style="text-align: center;"}
*Imagine each stick as a task you need to complete!*
{: style="text-align: center;"}


The creators of the Mikado method are Ola Ellnestam and Daniel Brolund, in their book [The Mikado Method](https://www.manning.com/books/the-mikado-method), they describe it as:

> "a process for surfacing hard-to-see dependencies in a codebase.
> It is very useful when you are trying to change a codebase, eliminate technical debt, and get things done while keeping focus on creating business value.”

![Mikado Method, the book](/assets/images/mikado-method/mikado-book.png)
{: style="text-align: center;"}

## Who cares?

If you've ever had to:

* Work on a bug
* Familiarize yourself legacy code
* Refactor a codebase

Then the Mikado method might be beneficial as a tool in your journey to familiarizing yourself with your codebase in a more structured manner, and being a more efficient developer! 

If you've ever tried to work with a codebase and in your exploration find everythings littered with compiler issues or runtime errors, this is a technique to avoid getting tangled in an absolute rats nest of dependency failures.

## How does it work?

The authors use the Mikado game as described earlier as a metaphor for extracting intertwined dependencies which should ideally lead you to the central issue. All this, while still having a runnable system to work with.


Knowing how the game works, we can see how it applies to the Mikado method!

It is a 4 step process consisting of:

1. Set a goal
2. Experiment
3. Visualize
4. Undo

In order to follow the above, you can start with just a pen and a piece of paper.

### Set a Goal

Here you set out what you wish to accomplish. Typically, this could be simply pulling a ticket from your backlog, either a feature or bugfix.

At the bottom of your page you need to draw a circle with the title of your goal in the middle.

![Mikado Goal](/assets/images/mikado-method/mikado-goal.jpg)
{: style="text-align: center;"}
*This is your Mikado!*
{: style="text-align: center;"}

### Experiment

The next step is to experiment. In terms of the game Mikado, you’re attempting to pick up a stick! However in the context of your codebase you’re going for the Mikado right now! So, with that in mind, the next step is to implement the goal naively. That is, write the code as if it would just work. 

If your naive code works and you have no issues, check it in and call it a day! Of course maybe, you want to include a test as well. ;)

Chances are however that you’re probably going to face some compiler issues (in a statically typed system) or the application won’t run correctly when you test it. Most days developers don’t like seeing compiler or application issues like these. However in this case, this is good news!

Implementing code naively like this and watching the errors show up, is related to Michael Feather’s phrase of “leaning on the compiler”. By naively implementing the goal you’re “leaning on the compiler”, your compiler / IDE will inform you of any errors along the way. In a dynamically typed language you may not have this luxury and may have to run the code directly for feedback.

The idea here is that the errors are providing feedback on what needs to be done next.

So at this point you’ve picked up a stick and moved a bunch of things, fortunately you get as many chances as you like to do this rather than 3 as in the game itself. Before you rejumble the sticks and dump em again you need to Visualize the experiment.


### Visualize

This part is not really in the game but it serves as a form of documentation. In fact, it represents the jumble of sticks that are laid in front of you from the perspective of your codebase as if it were a game of Mikado. Think about it, you tried to pick up the Mikado directly but you moved another stick (dependency) along the way!

You need to draw an arrow pointing away from your goal and in a circle write down the prerequisite that was blocking you from picking up your Mikado.

![Mikado Dependency](/assets/images/mikado-method/mikado-dependency.jpg)
{: style="text-align: center;"}

*This "stick" ::cough:: dependency ::cough:: is on top of your Mikado!*
{: style="text-align: center;"}

After visualizing the dependency you can now move onto the next step.

### Undo

Like in the game, when you attempt to pick up a stick that moves another one you need to jumble the sticks back up and start all over again!

If working with a VCS like git this can be as simple as:

`git checkout .`
{: style="text-align: center;"}

It might seem counterintuitive to undo the changes you just did, however, there is a purpose.  Look back at your piece of paper, you now have a new point to start from in order to achieve the original goal  (“double ~~rainbow~~ circle) you defined .

At this point, you now start from the new dependency you just drew and repeat steps 2 - 4. Congratulations, you now know the Mikado method!

If all has gone well you now have a dependency graph illustrating the changes you need to make in order to implement your feature or fix your bug. Not only that but they are cleanly separated by the changes that need to be made. Your graph could even lend itself to each node representing a git commit.

![Mikado Graph](/assets/images/mikado-method/mikado-graph.jpg)
{: style="text-align: center;"}


That was quick right? It’s not a silver bullet and by no means is supposed to represent a way for you to write correct software all the time but I find this an interesting tool.

By using the Mikado method you can work from the top-down and develop a dependency graph of the changes you need to make in order to deliver a feature or fix a bug. 

Worst case scenario, if you’ve gone down a rabbit hole and perhaps don’t get the results you were hoping for, you’ve visited your codebase in a structured manner which at the very least will have provided you with some familiarity with your codebase and you’ve understood some parts you were unfamiliar with previously. As developers, we actually spend most of our time reading rather than writing code.


### Conclusion

Is the above going to work all the time? Probably not. But I love coming across these types of ideas that are simple enough to help me rethink my process of moving towards a solution (remember red-green-refactor?) I imagine you won't score a goal each time. In fact I expect you may find yourself going down a path that does not help you achieve your goal in the best possible way, however, I wouldn't notch it up as a loss. You now have:

1) A form of documentation of the system 
2) Increased familiarity with the codebase in a more structured manner. 

Remember as developers, we actually spend most of our time reading versus developing, so in your Mikado journey if you don’t arrive at a solution it's not at a total loss!

The hardest part of all might be in defining a goal. That is essentially where the journey begins! The authors in the book recommend that if you’re finding it hard to decide on a goal then it may be a hint that you don’t have all the information you need to make a decision. You might need to talk to people involved to see if you’re missing something.

I like the idea of following the dependencies associated with the goal you want to achieve. By doing this you have a bottom up implementation plan rather than trying to force an implementation from the top-down. 

If you’ve found the above helpful, I’d recommend purchasing the book to support the authors. There is more in the book they go over such as grouping more complex dependencies, technical debt, restructuring patterns and emergent design.

I hope in the very least this has “picked-up” your interest in writing better software and becoming a more productive and efficient developer!

## References

* [http://mikadomethod.info/](http://mikadomethod.info/)
* [https://www.manning.com/books/the-mikado-method](https://www.manning.com/books/the-mikado-method)
* [https://www.methodsandtools.com/archive/mikado.php](https://www.methodsandtools.com/archive/mikado.php)
* [https://en.wikipedia.org/wiki/Mikado_(game)](https://en.wikipedia.org/wiki/Mikado_(game))
* [https://www.amazon.com/Working-Effectively-Legacy-Michael-Feathers/dp/0131177052](https://www.amazon.com/Working-Effectively-Legacy-Michael-Feathers/dp/0131177052)

