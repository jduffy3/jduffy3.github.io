# The Strategy Pattern

## What is it?

An object based, behavioral design pattern where the intent is to:

> *"Define a family of algorithms, encapsulate each one, and make them interchangeable"*

### [What does that mean?](https://www.youtube.com/watch?v=KsXfMU9oloQ)

When reading the GoF's Design Patterns book, it's important to understand *Class based* design patterns mean they are implemented through inheritance.

*Object based* design patterns on the other hand, are implemented with composition and help enforce a key design priniciple: 
> [*"Favor composition over inheritance."*](https://en.wikipedia.org/wiki/Composition_over_inheritance)

It's one of many design principles in OO and it might be something you hear so often, it can be easy to just accept the phrase itself and not understand it.

#### Why favor composition over inheritance?

Let's get inheritance out of the way first.

Inheritance is referred to as *white-box reuse* because the internals are visible to subclasses. 
This can lend itself to breaking encapsulation. 

Why? 

* By inheriting from a class, you have access to override member variables and functions (implementations) the author might not have intended others to do. 
* Inheritance *couples* the subclass to the parent class, so not only can your changes break encapsulation. Any change in the parent class can force the subclass to change, making it easier to break encapsulation again, or violate the [Open Closed Principle](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle).
* If not designed correctly can violate the [Liskov Substitution Principle](https://en.wikipedia.org/wiki/Liskov_substitution_principle). 
* In some languages, create the dreaded [diamond of death](https://en.wikipedia.org/wiki/Multiple_inheritance). 

Meanwhile in composition land...

Composition can be referred to as [*black-box reuse*](http://www.cs.unc.edu/~stotts/GOF/hires/chapAfso.htm) because the internal details of objects are hidden. This helps prevent yourself and others from breaking encapsulation and lends itself to another design principle: 

> *Program to interfaces, not implementations.*

Through composition, responsibilities are divided across separate class hierarchies separating what can change from what stays the same. Encapsulation is preserved through these hierarchies.
Anything that a class wants others to know is accessible through its interface only, in contrast to inheritance, where being able to override is potentially permitted. 

Separating class hierarchies might sound restrictive but in fact, it's really affording the developer *flexibility*.  
In regards to the Strategy pattern, this flexibility is being able to change a classes behavior. Or as defined by GoF in it's intent "*define a family of algorithms, encapsulate each one, and make them interchangeable*".

### When would I use it the Strategy pattern?

You might consider using the Strategy pattern when:

* Related classes differ only in their behavior. If so, break each behavior out into its own *Strategy* class.
* There are multiple conditional statements in your methods. Instead, move conditional branches into their own *Strategy* class.
* The algorithm (behavior) uses data that clients (classes) shouldn't know about. Use the strategy pattern to enforce / protect encapsulation.


Let's try and illustrate this pattern in code, first demonstrating how one might approach it via Inheritance.

## Code

Let's say we're building a game.
Our client wants a game where people can meet and greet each other.
Some characters are nice and agreeable, others are not. 
This behavior is not static though. Just like in the real world, our moods change and can influence how character greets someone.


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

It works. But the implemenation doesn't really capture how people greet each other. Moods are dynamic to people and influence how we greet each other. If we wanted to change Scrooge to be a `NicePerson` we'd have to construct them all over again. We'd even have to modify the variable `scrooge` to be a `var` instead of `val`. What I like about Kotlin, is that it now forces us to declare `scrooge` as the type `Person`. In Java, we always declare our types and this would've been the norm, but in this instance, to me, things are starting to smell.

e.g.

```kotlin
fun main() {
    var scrooge: Person = MeanPerson("Scrooge")
    scrooge.greet(NicePerson("Keanu"))

    scrooge = NicePerson("Scrooge")
}
```

### via Composition (the Strategy pattern good idea)

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

With the above the output is no different. 
But now, we've made things more *flexible*. We have injected the behavior (the Strategy *`Greeter`*) through the constructor via composition. 

With a few more changes, we can add another method *`change`* that takes in a *`Greeter`* and updates the member variable via method composition. This enables us to change the *behavior* (family of algorithm) during runtime.

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

The output now looks like 

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

That is the definition of the strategy pattern!

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
