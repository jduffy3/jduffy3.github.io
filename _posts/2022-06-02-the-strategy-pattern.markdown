# The Strategy Pattern

## What is it?

An object based, behavioral design pattern with the intent to:

> *"Define a family of algorithms, encapsulate each one, and make them interchangeable"*

## What does that mean?

When reading the GoF's Design Patterns book the authors distinguish *Class* and *Object* based patterns. 
Class based behavioral patterns are typically implemented via inheritance, while Object based patterns use composition. 

This also introduces a key design priniciple: 
> [*"Favor composition over inheritance."*](https://en.wikipedia.org/wiki/Composition_over_inheritance)

### Why favor composition over inheritance?

First, let's discuss inheritance.

Inheritance is referred to as *white-box reuse* because a classes internals are visible to subclasses.
They are visible because they are defined *within* a family of hierarhcies.
However, this can lend itself to breaking encapsulation.

How? 

1. By inheriting from a class, you have the potential to override member variables and methods the author might not have intended others to do.
2. Inheritance *couples* a subclass to it's parent class. Any change in the parent class can then force the subclass to change. 

#### Composition

Composition is sometimes referred to as [*black-box reuse*](http://www.cs.unc.edu/~stotts/GOF/hires/chapAfso.htm). This is because the internal details of objects are hidden. This helps preserve encapsulation. 

How?

Responsibilities are divided *across* class hierarchies.
Because these responsibilities are divided across hierarchies classes don't have access to another classes internal details. 
Anything that is accessible is because the author has exposed it through the classes public interface.
This is safer than inheritance because the potential to override members that the author may not have intended others to do could lead to unintened consequences.

While separating class hierarchies might sound restrictive, it really affords the developer *flexibility*.  
In the Strategy pattern, this is the flexibility to change a classes behavior. 
Or as the patterns definition puts so succinctly "*define a family of algorithms, encapsulate each one, and make them interchangeable*".

### When would I use the Strategy pattern?

You might consider the Strategy pattern when:

* Related classes differ only in their behavior. If so, break each behavior out into its own *Strategy* class.
* There are multiple conditional statements in your methods. Instead, move conditional branches into their own *Strategy* class.
* The algorithm (behavior) uses data that clients (classes) shouldn't know about. Use the strategy pattern to enforce / protect encapsulation.

Let's try and illustrate this pattern in code, first demonstrating how one might approach it via Inheritance.

## Code

Let's say we're building a game.
Our client wants a game where people can meet and greet each other.
Some characters are nice and agreeable, others are not. 
This behavior is not static though. Just like in the real world, our moods change and can influence how a character greets someone.


### via Inheritance (bad idea)

```kotlin
fun main() {
    val keanu = NicePerson("Keanu")
    val scrooge = MeanPerson("Scrooge")
    
    print("Keanu: ")
    keanu.greet(scrooge)

    print("Scrooge: ")
    scrooge.greet(keanu)
}

abstract class Person(val name: String){
    abstract fun greet(anotherPerson: Person)
}

class MeanPerson(name: String): Person(name){
    override fun greet(anotherPerson: Person) {
        println("What do you want?")
    }
}

class NicePerson(name: String): Person(name){
    override fun greet(anotherPerson: Person) {
        println("Hi ${anotherPerson.name}. Nice to meet you.")
    }
}
```

This would print the following when we run `main()`

```
Keanu: Hi Scrooge. Nice to meet you.
Scrooge: What do you want?
```

It works... But the implemenation doesn't really capture how people greet each other. Moods are dynamic and influence how we greet each other. If we wanted Scrooge to be a `NicePerson` we'd have to construct him all over again, however, Scrooge isn't being reborn we're just trying to change behavior! 
If we wanted to re-construct Scrooge, Kotlin would forces us to write our code a little differently. We'd have to modify the variable `scrooge` to be a `var` instead of `val` declare `scrooge` as the type `Person`. 
In Java, we always declare our types and this would've been the norm, but in this instance, to me, it fells like Kotlin is trying to tell us things are starting to smell.

e.g.

```kotlin
fun main() {
    var scrooge: Person = MeanPerson("Scrooge")
    scrooge.greet(NicePerson("Keanu"))

    //Is this the same scrooge? If we had other state we were interested in, this would be gone!
    scrooge = NicePerson("Scrooge")
}
```

### via Composition (the Strategy pattern good idea)

With the inheritance example above, we had a Person class and all it's implementations were a Subclass of Person. If it was a more complex class and we were trying to add more behavior the risk of subclasses introducing changes that weren't orignally designed could make this a nightmare to maintain. This is also demonstrating that all classes are *within* a familiy of hierarchies.

Approaching this from the perspective of composition, we will now divide responsibility *across* class hierarchies by defining two class hierarchies *Person* and *Greeter*. This will help keep all things Greet related separate from all things Person related and help protect us from breaking encapsulation

```kotlin
class Person(val name: String, private var greeter: Greeter){
    fun greet(anotherPerson: Person) {
        println(greeter.greet(anotherPerson))
    }
}

interface Greeter {
    fun greet(anotherPerson: Person): String
}

class Nice: Greeter {
    override fun greet(anotherPerson: Person): String {
        return "Hi ${anotherPerson.name}. Nice to meet you."
    }
}

class Mean: Greeter {
    override fun greet(anotherPerson: Person): String {
        return "What do you want?"
    }
}
```

Although the output is the same as before, things are different for the developer. We've made the design more *flexible*. We achieved this by injecting the behavior (i.e. *`Greeter`* strategy) through the constructor via composition. 


We can demonstrate this fliexibility if we add another method *`change`* that takes a *`Greeter`* via method composition. This enables us to change the *behavior* (family of algorithm) during runtime.

```kotlin
fun main() {
    val scrooge = Person("Scrooge", Mean())
    val keanu = Person("Keanu", Nice())

    print("Keanu: ")
    keanu.greet(scrooge)
    print("Scrooge: ")
    scrooge.greet(keanu)

    println("...")
    //changing behavior at run-time
    keanu.change(Mean())
    scrooge.change(Nice())

    print("Keanu: ")
    keanu.greet(scrooge)
    print("Scrooge: ")
    scrooge.greet(keanu)
}

class Person(val name: String, var greeter: Greeter){
    fun greet(anotherPerson: Person ) {
        println(greeter.greet(anotherPerson))
    }
    fun change(greeter: Greeter) {
        this.greeter = greeter
    }
}

interface Greeter {
    fun greet(anotherPerson: Person): String
}

class Nice: Greeter {
    override fun greet(anotherPerson: Person): String {
        return "Hi ${anotherPerson.name}. Nice to meet you."
    }
}

class Mean: Greeter {
    override fun greet(anotherPerson: Person): String {
        return "What do you want?"
    }
}
```

The output now shows...

```
Keanu: Hi Scrooge. Nice to meet you.
Scrooge: What do you want?
...
Keanu: What do you want?
Scrooge: Hi Keanu. Nice to meet you.
```


## Conclusion

With the above we've now done the following:

* Defined a family of algorithms (`Greeter`)
* Encapsulated each one (`Mean`, `Nice`)
* Made them interchangable (via method and constructor injection)

This is the definition of the strategy pattern!

### Other thoughts

Some languages today e.g. Kotlin make it harder to accidentally break encapsulation:

* Classes to be extended have to be declared `open`
* Methods to be overridden have to be declared as `open` or `override` 

It can make some of the examples a bit contrived, however, the focus here is to demonstrate the pattern not the language and I did not want to write this in Java! So don't be too critical if I'm bending the language to demonstrate the example for the benefit of simplicity.

### Patterns and design principles in general

The Strategy Pattern may be simpler to understand compared to the [Template Method]({% post_url 2021-11-17-the-template-method-pattern %}).
It's the first pattern in the Head First book and demonstrates fundamental OO principles one should always keep in mind:

> *Encapsulate what varies*

> *Favor composition over inheritance*

> *Program to interfaces, not implementations*

The Template Method may be easier to spot in the wild compared to Strategy as it's statically defined (via Inheritance).
However, Strategy can offer a lot more flexibility in regards to design and run-time behavior.

## References

* [Design Patterns (GOF)](https://www.amazon.com/Design-Patterns-Object-Oriented-Addison-Wesley-Professional-ebook/dp/B000SEIBB8)
* [Head First Design Patterns](https://www.amazon.com/Head-First-Design-Patterns-Object-Oriented-ebook-dp-B08P3X99QP/dp/B08P3X99QP)
