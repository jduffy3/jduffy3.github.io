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

## Seriously, what is it?

Class based design patterns implement themselves via inheritance. 
Object based design patterns ["favor composition over inheritance"](https://en.wikipedia.org/wiki/Composition_over_inheritance).
You might have heard heard your co-workers say that last part of that sentence a million times. It's a key design principle in OO.

> *Favor composition over inheritance*

By favoring composition over inheritance you allow for more *flexibility*. 
Inheritance, if not designed correctly can cause some issues like:

* Violate the [Liskov Substitution Principle](https://en.wikipedia.org/wiki/Liskov_substitution_principle). 
* In some languages, the dreaded [diamond of death](https://en.wikipedia.org/wiki/Multiple_inheritance). 

But why *favor composition*?

### Composition

Inheritance is referred to as *white-box reuse*. 
This is because the internals are visible to subclasses, this breaks encapsulation. 
Why? By inheriting from a class you have access to member variables and can override functions you the author might not have intended others to do.
Inheritance couples the subclass to the parent class, so any change in the parent class can force the subclass to change. This potentially breaks encapsulatuion and our goal is to not break anything!

*Black-box reuse* is referred to as composition because internal details of objects are hidden. Details are accesssed through their interfaces only. 
This is a good thing because it doesn't break encapsulation and is related to the design principle: 

> *Program to interfaces, not implementations*

So in essence, dividing responsibilities across separate class hierarchies, we favor composition over inheritance.
We are drawing a boundary that separates what changes from what stays the same and preserving encapsulation.
Anything that a class wants others to know is exposed through its interface (or at least should be!).

Let's try and illustrate this concept with code.

## Code

Let's say we're building a game. 
Our client wants game where people can meet and interact.
Some characters are nice and agreeable and others are not. 
It can depend on their mood or the type of character they are.
Just like in the real world, moods can change. Already when hearing something can change, this is the time to note you want to encapsulate everything around that.

In relation to the Strategy Pattern lets encapsulate that.

```kotlin
class Person(val name: String, private var: mood: Mood) {

  fun say(sentence: String) {
    println(sentence)
    mood.express()
  }

  fun greet() {
    say("Hi, my name is $name")
  }

  fun gratitude() {
    say("Thank you")
  }

  fun endConversation() {
    say("Goodbye")
  }

  fun change(mood: Mood) {
    this.mood = mood
  }
}

interface Mood {
  fun express()
}

class Excited : Mood {
  fun express() {
    println("Yay!!!")
  }
}
```

## Conclusion

The Strategy Pattern may be simpler to understand compared to the [Template Method]({% post_url 2021-11-17-the-template-method-pattern %}) 
it's the first pattern in the Head First series and demonstrates fundamental OO principles one should always keep in mind:

> Encapsulate what varies

> Favor composition over inheritance

> Program to interfaces, not implementations

The Template Method may be easier to spot in the wild compared to Strategy as it's statically defined.
However, Strategy can offer a lot more flexibility in design-time as well as run-time.

## References

* [Design Patterns (GOF)](https://www.amazon.com/Design-Patterns-Object-Oriented-Addison-Wesley-Professional-ebook/dp/B000SEIBB8)
* [Head First Design Patterns](https://www.amazon.com/Head-First-Design-Patterns-Object-Oriented-ebook-dp-B08P3X99QP/dp/B08P3X99QP)
