# The Template Method Pattern

## What is it?

A class behavioral design pattern.

## Seriously, what is it?

Templates live among us everywhere. They are a set of instructions that define how something is done. If you've ever tried following a recipe or tutorial, congratulations, you've come across the template method pattern! 

The [Gang of Four](http://wiki.c2.com/?GangOfFour) describe it as:

> *The skeleton of an algorithm in an operation, defferring some steps to subclasses. Template Method lets subclasses redefine certain steps of an algorithm without changing the algorithms structure.*

Real world examples of the template method pattern could be:

* Morning routine
* Exercise routine

When writing, I try to be mindful of duplcation. Generally, I wouldn't  like seeing the word routine duplicated above in the examples. 
However, I think it serves quite well in explaining the template pattern, because the definition of [routine](https://www.lexico.com/en/definition/routine) reinforces the intent of the pattern:

> *a sequence of actions regularly followed; a fixed program.*


So lets work with the Exercise example...

### Example exercise routine

When working out we normally do the following. Or at least we should!

1. warm up
2. workout
3. cool down

If I forget to warm up, I could tear a muscle.
If I don't cool down, it can cause blood to pool in in the lower extremeties, and faint. 
As a result, it's important that the above steps are followed in order.

The above is our template!

How about we go for a run? Described in terms of our template it could be:

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

Our template could look like:

```kotlin
abstract class ExerciseRoutine(private val warmupTime: Int, private val workoutTime: Int, private val coolDownTime: Int) {
    //this is our template method
    fun routine(person: Person) {

        warmup(person, warmupTime)

        workout(person, workoutTime)

        coolDown(person, coolDownTime)
    }

    abstract fun warmup(person: Person, time: Int)
    abstract fun workout(person: Person, time: Int)
    abstract fun coolDown(person: Person, time: Int)
}
```

Now for the implementation.

### Encapsulate what varies

Now's the time to implement the above abstract class. It could look like:

```kotlin
class RunningExercise(warmup: Int, workout: Int, coolDown: Int) : ExerciseRoutine(warmup, workout, coolDown) {

    override fun warmup(person: Person, time: Int) {
        person.stretch(time)
    }

    override fun workout(person: Person, time: Int) {
        person.run(time)
    }

    override fun coolDown(person: Person, time: Int) {
        val walkTime = if(time % 2 != 0) (time + 1) / 2 else time / 2
        person.briskWalk(walkTime)

        val stretchTime = time - walkTime
        person.stretch(stretchTime);
    }
```

The only real complexity above, is determining how many minutes to walk versus how many to stretch. 
That's not really the part to focus on, and I'm sure you can think of a better way than what I did!

The key takeway is that we've defined a fixed order of how something is performed in the `RunningExercise` class.
We are also demonstrating two design principles
* Encapsulate what varies
* [Don't repeat yourself](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)

What varies is *what* exercise a person is doing (i.e. Running). Developers can add new exercises and be sure they align to the template defined in the `ExerciseRoutine` class.

What isn't duplicated is the order the steps are done in. This is defined in our *routine* method (the template).


The only thing missing now is the Person class. 

```kotlin
class Person(private val name: String) {
    fun run(time: Int){
        println("$name is running for $time minutes!")
    }

    fun briskWalk(time: Int){
        println("$name is walking briskly for $time minutes!")
    }

    fun stretch(time: Int) {
        println("$name is stretching for $time minutes!")
    }

    fun perform(exercise: ExerciseRoutine) {
        exercise.routine(this)
    }
}
```

We could execute the above as:

```kotlin
fun main() {
    val person = Person("Joe")
    val aRun = RunningExercise(10, 45, 15)
    person.perform(aRun)
}
```

With the above code we can be sure that people are performing exercises correctly and our developers can easily add new exercises to the application!

## Conclusion

I find the Template Method Pattern a good starter for learning about Design Patterns. 
It is relatively easy to understand yet provides powerful capabilities.

For learning about Design Patterns the two most important books I've read (and re-read) are the classic GoF book and Head First series on Design Patterns.
I've read other ones but I always come back to these two.

They explain further ideas in the template pattern such as introducing hooks, where you can define a method that a sub class can optionally override. 
This allows you to create clasess that may or may not perform a particular operation. Depending on the complexity of what you're trying to model, you can introduce conditional checks and member variables that are also inherited. The possible implementations are as numerous as the variations of this pattern you'll see in the wild! Again, the takeaway is not the implementation above but the pattern itself.

I would highly recommend reading (and re-reading) them! The above code example is by no means definitive, in fact you might argue with me the [Strategy pattern](https://en.wikipedia.org/wiki/Strategy_pattern) would be better suited here, and you *could* be right! The takeaway is recognizing the pattern.

## References

* [Design Patterns (GOF)](https://www.amazon.com/Design-Patterns-Object-Oriented-Addison-Wesley-Professional-ebook/dp/B000SEIBB8)
* [Head First Design Patterns](https://www.amazon.com/Head-First-Design-Patterns-Object-Oriented-ebook-dp-B08P3X99QP/dp/B08P3X99QP)
* [Why warming up and cooling down is important](https://www.tricitymed.org/2016/12/warming-cooling-important/)
