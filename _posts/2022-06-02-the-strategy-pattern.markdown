# The Strategy Pattern

## What is it?

An object based, behavioral design pattern to:

> *"Define a family of algorithms, encapsulate each one, and make them interchangeable"*

## What does that mean?

In the GoF's Design Patterns book the authors distinguish between *Class* and *Object* based patterns. 
Class based behavioral patterns are implemented with inheritance, while Object based patterns favor composition. 

One principle of OOD is to:
> [*"Favor composition over inheritance."*](https://en.wikipedia.org/wiki/Composition_over_inheritance)

### Why favor composition over inheritance?

Inheritance is referred to as *white-box reuse* because a classes internals are visible to their subclasses.
Classes are defined *within* a family of hierarhcies that extend a common parent class, and while it can help keep things DRY, inheritance can expose a parents implementation details to it's subclass which has led to some arguing ["inheritance breaks encapsulation"](https://www.cs.tufts.edu/comp/150CBD/readings/snyder86encapsulation.pdf).

How? 

1. By inheriting from a class, you have the potential to override member variables and methods the author might not have intended others to do.
2. Inheritance *couples* a subclass to it's parent class. Any change in the parent class can force its subclasses to change. If this change is not applicable to a subclass (e.g. implementing a no-op is a typical smell, or throwing an exception for an "unsupported method") can be a violation of the [Interface Segregation Principle](https://en.wikipedia.org/wiki/Interface_segregation_principle) which states "no code should be forced to depend on methods it does not use". 
3. In some languages, sub classes can shadow parent class variables and introduce errors into the system.

The above is not intended to discourage the use of inheritance but to highlight some of the potential problems that can surface if used incorrectly.

#### Composition

Composition is referred to as [*black-box reuse*](http://www.cs.unc.edu/~stotts/GOF/hires/chapAfso.htm) because an objects internal details are hidden.
This helps preserve encapsulation by removing exposure to implementation details. 

How?

Responsibilities are divided *across* class hierarchies, and so classes don't have access to the internal details of other classes.
Anything that is accessible to clients, is because it has been explicitly exposed through it's public facing interface, adhering (in a sense) to another design principle:

> *Program to interfaces, not implementations*

By programming to an interface, your clients are depending on abstractions (or should be!). This provides you, the developer, the flexibility to change implementation details and not have to worry about breaking existing contracts with those clients. 

In relation to the Strategy pattern, this flexibility is the ability to change a classes behavior.

### When would I use the Strategy pattern?

You might consider the Strategy pattern when:

* Related classes differ only in their behavior. If so, break each behavior out into its own *Strategy* class.
* There are multiple conditional statements in your methods. Instead, move conditional branches into their own *Strategy* class.
* An algorithm (behavior) uses data that clients (classes) shouldn't know about. Use the Strategy pattern to enforce / protect encapsulation.

Let's try and illustrate this pattern in code, first demonstrating how one might approach it via Inheritance.

## Code

Imagine our client wants a game where people can meet and greet each other.
Some characters are nice and others are mean... for the time being.
This behavior is not static though. Just like in the real world, moods change and can influence how a character greets someone.

### via Inheritance (bad idea)

```kotlin
fun main() {
    val keanu = NicePerson("Keanu")
    var scrooge: Person = MeanPerson("Scrooge")

    println(keanu.greet(scrooge))
    println(scrooge.greet(keanu))
}

abstract class Person(val name: String){
    abstract fun greet(anotherPerson: Person): String
}

class MeanPerson(name: String): Person(name){
    override fun greet(anotherPerson: Person): String {
        return "I'm $name. What do you want?"
    }
}

class NicePerson(name: String): Person(name){
    override fun greet(anotherPerson: Person): String {
        return "Hi ${anotherPerson.name}, my name is $name. Nice to meet you."
    }
}
```

This prints the following when we run `main()`

```
Hi Scrooge, my name is Keanu. Nice to meet you.
I'm Scrooge. What do you want?
```

It works... But the implemenation doesn't really capture how people greet each other. 
Moods are dynamic and influence how we greet each other. 
If we wanted Scrooge to be a `NicePerson` we'd have to construct a new instance of `NicePerson`. The only way to do this in Kotlin is to modify the variable `scrooge` to be a `var` instead of `val` and declare `scrooge` as the type `Person`. 

Or we could declare two people with the name "Scrooge" with different moods. But that REALLY doesn't make sense!
```kotlin
  // bonkers
  val meanScrooge = MeanPerson("Scrooge")
  val niceScrooge = NicePerson("Scrooge")
```

Either case, we are creating new objects and really we want to change behavior. Creating new objects also means we are losing state. In this case it's just a name which we can easily provide in the constructor however if this was a more complex class and had state that was dynamic to the lifecycle of an object we would lose all of that state! 

e.g.

```kotlin
fun main() {
    var scrooge: Person = MeanPerson("Scrooge")
    scrooge.greet(NicePerson("Keanu"))

    //If scrooge had other state we were interested in, this would now be gone!
    scrooge = NicePerson("Scrooge")
}
```

### via Composition (the Strategy pattern good idea)

In the inheritance example above, we defined an abstract `Person` class and all it's implementations are a subclass of `Person`. 
This demonstrated classes being defined *within* a familiy of hierarchies. 

Approaching this from the perspective of composition, we will now divide the responsibilities *across* two class hierarchies by defining a *Person* class and *Greeter* interface. This will help keep all things Greet related separate from all things Person related.

```kotlin
class Person(val name: String, private var greeter: Greeter){
    fun greet(anotherPerson: Person): String {
        return greeter.greet(this, anotherPerson)
    }
}

interface Greeter {
    fun greet(self: Person, anotherPerson: Person): String
}

class Nice: Greeter {
    override fun greet(self: Person, anotherPerson: Person): String {
        return "Hi ${anotherPerson.name} my name is ${self.name}. Nice to meet you."
    }
}

class Mean: Greeter {
    override fun greet(self: Person, anotherPerson: Person): String {
        return "I'm ${self.name}. What do you want?"
    }
}
```

Although the output is the same as before, things are different for the developer. We've made the design more *flexible*. We achieved this by injecting the behavior (i.e. *`Greeter`* strategy) through the constructor via composition. 

We can demonstrate this fliexibility once more by adding another method *`change`* that takes a *`Greeter`* via method composition. This enables us to change the *behavior* (family of algorithm) during runtime in order to dynamically change a persons mood.

```kotlin
fun main() {
    val scrooge = Person("Scrooge", Mean())
    val keanu = Person("Keanu", Nice())

    println(keanu.greet(scrooge))
    println(scrooge.greet(keanu))

    println("...")
    //changing behavior at run-time
    keanu.change(Mean())
    scrooge.change(Nice())

    println(keanu.greet(scrooge))
    println(scrooge.greet(keanu))
}

class Person(val name: String, private var greeter: Greeter){
    fun greet(anotherPerson: Person): String {
        return greeter.greet(this, anotherPerson)
    }
    fun change(greeter: Greeter) {
        this.greeter = greeter
    }
}

interface Greeter {
    fun greet(self: Person, anotherPerson: Person): String
}

class Nice: Greeter {
    override fun greet(self: Person, anotherPerson: Person): String {
        return "Hi ${anotherPerson.name} my name is ${self.name}. Nice to meet you."
    }
}

class Mean: Greeter {
    override fun greet(self: Person, anotherPerson: Person): String {
        return "I'm ${self.name}. What do you want?"
    }
}
```

The output now shows...

```
Hi Scrooge my name is Keanu. Nice to meet you.
I'm Scrooge. What do you want?
...
I'm Keanu. What do you want?
Hi Keanu my name is Scrooge. Nice to meet you.
```

## Conclusion

With the above we've now done the following:

* Defined a family of algorithms (`Greeter`)
* Encapsulated each one (`Mean`, `Nice`)
* Made them interchangable (via method and constructor injection)

This is the definition of the strategy pattern!

### Patterns and design principles in general

The Strategy Pattern may be simpler to understand compared to the [Template Method]({% post_url 2021-11-17-the-template-method-pattern %}).
It's the first pattern in the Head First book and demonstrates fundamental OO principles one should strive to keep in mind:

> *Encapsulate what varies*

> *Favor composition over inheritance*

> *Program to interfaces, not implementations*

The Template Method may be easier to spot in the wild compared to Strategy as it's statically defined (via Inheritance), the Strategy pattern can offer a lot more flexibility in regards to design and run-time behavior.

## References

* [Design Patterns (GOF)](https://www.amazon.com/Design-Patterns-Object-Oriented-Addison-Wesley-Professional-ebook/dp/B000SEIBB8)
* [Head First Design Patterns](https://www.amazon.com/Head-First-Design-Patterns-Object-Oriented-ebook-dp-B08P3X99QP/dp/B08P3X99QP)
