# The Template Method Pattern

## What is it?

A class behavioral design pattern.

## Seriously, what is it?

Templates live among us everywhere. They are a set of instructions that describe how something is done. If you've ever tried following a recipe or tutorial, congratulations, you've come across the template method pattern! 

The [Gang of Four](http://wiki.c2.com/?GangOfFour) describe it as:

> *The skeleton of an algorithm in an operation, defferring some steps to subclasses. Template Method lets subclasses redefine certain steps of an algorithm without changing the algorithms structure.*

Real world examples of the template method pattern could be:

* Morning routine
* Exercise routine

Generally, I wouldn't like seeing the word routine duplicated above in the examples. 
However, I think it serves quite well in explaining the template pattern, because the definition of [routine](https://www.lexico.com/en/definition/routine) reinforces the intent of the pattern:

> *a sequence of actions regularly followed; a fixed program.*


So lets work with the Exercise example...

### Example exercise routine

When working out we normally do the following. Or at least we should!

1. warm up
2. workout
3. cool down

If I forget to warm up, I could tear a muscle.
If I don't cool down, it can cause blood to pool in in the lower extremeties, and I faint. 
As a result, it's important that the above steps are followed in order.

The above is our template!

So without fainting, let's go for a run! 
In terms of our template it could be:

1. **Warm up**
    * Stretch
2. **Workout**
    * Run!
3. **Cool down**
    * Brisk walking
    * Stretch

Alright, code it!

## The code

There are 2 steps to implementing this pattern

1. Define the template
2. Encapsulate what varies


### Define the template

You define the template (skeleton) of your method (operation) in an *abstract class* (since this is a class behavioral pattern).

How about:

```kotlin
typealias Minutes = Int
abstract class ExerciseRoutine(private val warmupTime: Minutes, private val workoutTime: Minutes, private val coolDownTime: Minutes) {
    fun routine(person: Person) {

        warmup(person, warmupTime)

        workout(person, workoutTime)

        coolDown(person, coolDownTime)
    }

    abstract fun warmup(person: Person, time: Minutes)
    abstract fun workout(person: Person, time: Minutes)
    abstract fun coolDown(person: Person, time: Minutes)
}
```

We've defined our template (skeleton) method in terms of a Person. 
We want to make sure they are warmed up, worked out and cooled down!
The constructor accepts 3 parameters that define how long each step should last (minutes).

But what is a Person? Let's define that now:

```kotlin
class Person(private val name: String) {
    fun run(time: Minutes){
        println("$name is running for $time minutes!")
    }

    fun briskWalk(time: Minutes){
        println("$name is walking briskly for $time minutes!")
    }

    fun stretch(time: Minutes) {
        println("$name is stretching for $time minutes!")
    }

    fun perform(exercise: ExerciseRoutine) {
        exercise.routine(this)
    }
}
```

We still need to go for a run though.

### Encapsulate what varies - going for a run

```kotlin
class RunningExercise(warmup: Minutes, workout: Minutes, coolDown: Minutes) : ExerciseRoutine(warmup, workout, coolDown) {

    override fun warmup(person: Person, time: Minutes) {
        person.stretch(time)
    }

    override fun workout(person: Person, time: Minutes) {
        person.run(time)
    }

    override fun coolDown(person: Person, time: Minutes) {
        val walkTime = if(time % 2 != 0) (time + 1) / 2 else time / 2
        person.briskWalk(walkTime)

        val stretchTime = time - walkTime
        person.stretch(stretchTime);
    }
}
```

The only real complexity above is determining how many minutes to walk versus how many to stretch, but that's not the part to focus on (and I'm sure you can think of a better way than what I did!).

The main thing is our template (skeleton) method `routine` has defined the steps. 
How those steps are performed is delegated to the `RunningExercise` class.

We're now demonstrating two design principles

* Encapsulate what varies
* [Don't repeat yourself](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)

What varies is *what* exercise a person is doing (i.e. Running). Developers can add new exercises and only concern themselves with what is performed.
What isn't duplicated is the order the steps are done in. This is defined in our *routine* method (the template).

We can run the above as:

```kotlin
fun main() {
    val joe = Person("Joe")
    val exercise = RunningExercise(10, 45, 15)
    joe.perform(exercise)
}
```

## Conclusion

I find the Template Method Pattern a good starter for learning about Design Patterns. 
It is relatively easy to understand yet provides powerful capabilities.

For learning about Design Patterns the two most important books I've read (and re-read) are the classic GoF book and Head First series on Design Patterns.
I've read other ones but I always come back to these two.

In the books, they explain other concepts within the template pattern such as hooks. Where you define a method that a sub class can optionally override. 

The possible implementations are as endless as the variations of this pattern you'll see in the wild! 

The takeaway is not necessarily the implementation of the Design Pattern itself, but being able to recognize the pattern and implement it when needed. 
In your journey as a developer you may have to navigate a large codebase, or design a solution to some problem. Being able to identify these patterns reduces your cognitive load, implementing them when appropriate, hopefully pushes you to a more elegant solution!

## References

* [Design Patterns (GOF)](https://www.amazon.com/Design-Patterns-Object-Oriented-Addison-Wesley-Professional-ebook/dp/B000SEIBB8)
* [Head First Design Patterns](https://www.amazon.com/Head-First-Design-Patterns-Object-Oriented-ebook-dp-B08P3X99QP/dp/B08P3X99QP)
* [Why warming up and cooling down is important](https://www.tricitymed.org/2016/12/warming-cooling-important/)
