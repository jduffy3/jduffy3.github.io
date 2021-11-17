# The Strategy Pattern

## What is it?

An object behavioral design pattern.

The Gang of Four describe it's intent as:

> *"Define a family of algorithms, encapsulate each one, and make them interchangeable"*

## When would I use it?

Smells like you need the Strategy pattern when:

* Related classes differ only in their behavior
* Multiple conditional statements in your methods
* The algorithm (behavior) uses data that clients (classes) shouldn't know about.

## WTH does that mean?

Usually class based design patterns implement themselves via inheritance. 
Object based design patterns [favor composition over inheritance](https://en.wikipedia.org/wiki/Composition_over_inheritance).
You might have heard heard your co-workers say this a million times. It's a key design principle in OO.

> Favor composition over inheritance

By favoring composition over inheritance you allow for more *flexibility*. 
Inheritance can become a tricky beast to tame. 
You should also strive to not violate the [Liskov Substitution Principle](https://en.wikipedia.org/wiki/Liskov_substitution_principle). 
In some other languages you might even fall prey to the [diamond of death](https://en.wikipedia.org/wiki/Multiple_inheritance). 
But I'd rather not go down that rabbit hole here...

But that still doesn't get me out of not talking about composition over inheritance.

Inheritance is referred to as *white-box reuse*. 
This is because the internals are often times visible to subclasses due to inheritance, this increases the possibility of breaking encapsulation. 
By inheriting from a class you have access to member variables and overriding functions you might not have originally intended when designing your parent class.
The subclass is so coupled to the parent class, that any change in the paren can force the subclass to change.

*Black-box reuse* is often referred to as composition. 
This is because no internal details of objects are visible and so objects are accesssed through their interfaces only. 
This is a good thing because it doesn't break encapsulation and is a design principle known as:

> program to interfaces, not implementations

I think that's good enough to know for now. Let's show some code.

## Code

Let's say we're building a game. Our client wants an online game where people can meet and interact.
In the game there are a few NPC characters.
Some characters are nice and agreeable and others are not. It can depend on their mood or the type of character they are.


Some people in the game are 
 game where people interact with one another.
In the game, people have names. Some people are nice, some people are not nice. 
Nice people do favors for someThey do favors for others and have moods like we do.

### Bad Idea


### Good Idea

## Conclusion

The Strategy Pattern is probably simpler to understand than the [Template Method]({% post_url 2021-11-17-the-template-method-pattern %}).
It's the first pattern in the Head First series and demonstrates fundamental OO principles I try to keep in mind whenever writing software.

If anything remember

> Encapsulate what varies

> Favor composition over inheritance

> Program to interfaces, not implementations

## References

* [Design Patterns (GOF)](https://www.amazon.com/Design-Patterns-Object-Oriented-Addison-Wesley-Professional-ebook/dp/B000SEIBB8)
* [Head First Design Patterns](https://www.amazon.com/Head-First-Design-Patterns-Object-Oriented-ebook-dp-B08P3X99QP/dp/B08P3X99QP)
