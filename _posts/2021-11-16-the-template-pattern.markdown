# The Template Pattern

## What is it?

A behavioral design pattern.

## Seriously, what is it?

Templates live among us everywhere, they are a set of instructions that define how something is done. If you've ever tried following a recipe or tutorial, congratulations, you've come across the template pattern! Well, kinda... Those were specific instructions with no variation. That doesn't sound very [open to extension and closed for modification](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle).

> The template pattern allows you to define a set of instructions and let subclasses define how particular instructions are executed.

So what would be a real example of a template pattern? Some examples are:

* Morning routine
* Workout routine

The key concept is that you're describing an ordered set of instructions. In the examples above I intuitively added routine to the end of each one. When writing in general, I try to be mindful of duplcation. Generally, I wouldn't  like seeing the word routine  duplicated like that. However, I think in this instance it serves explaining the template pattern quite well. The definition of [routine](https://www.lexico.com/en/definition/routine) even tells you:

> a sequence of actions regularly followed; a fixed program.

### Workout routine - real world example

When working out we normally do the following.  Or at least we should.

1. warm up
2. workout
3. cool down

If I for instance leave out warming up, I could tear a muscle. If I don't cool down, you can cause blood to pool in in the lower extermities, this might cause you to faint. As a result, it's importatnt that the above steps are followed in order.

So the above template for a run might look like

#### Warm up

1. Chest
2. Quads
3. Calves

#### Workout

* Run!

#### Cool down

1. Brisk walking
2. Same stretches as above
2. Foam rolling

## How does it work?

There are 2 steps to implementing this pattern

1. Define the template
2. Implement the variation


### Define the template

You define a "template" or skeleton of how your class is going to work.
Typically in Object Oriented terms this means defining an *abstract class*

Anything that is common to all implementations are defined and if required, implemented within the abstract class.

When writing software you've hopefully heard the phrase ["Don't repeat yourself"](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself). The template pattern is something that helps you achieve maintain this principle.  

For example we could define the template as

```kotlin
abstract class ExerciseRoutine() {
  fun exercise(warmupMinutes: Int, workoutMinutes: Int, coolDownMinutes: Int) {
    warmUp(warmupMinutes)

    workout(workoutMinutes)

    coolDown(coolDownMinutes)
  }

  abstract fun warmUp(minutes: Int)
  abstract fun workout(minutes: Int)
  abstract fun coolDown(minutes: Int)
}
```

Everything that is common to the template or routine is defined in the method `exercise`

Now for the implementation.


### Implement the variation

Here we define the concrete impelementations of this class. For example it could look like

```kotlin
class RunningExercise(private val runner: Runner) : ExerciseRoutine() {

  override fun warmUp(minutes: Int) {
    runner.stretch(minutes)
  }

  override fun workout(minutes: Int) {
    runner.run(minutes)
  }

  override fun coolDown(minutes: Int) {
    val minutesToWalk = if(minutes % 2 != 0) (minutes + 1) / 2 else minutes / 2
    val minutesToStretch = minutes - minutesToWalk
    runner.briskWalk(minutesToWalk)
    runner.stretch(minutesToStreatch);
  }
}
```

The only real complexity above, is determining how many minutes to Walk versus how many to stretch again. That's not really the part to focus on, and I'm sure you can think of a better way than what I chose.

The takeway here is that we've defined a set of instructions that will be executed in order. For example our main code would now look like:

```kotlin
class Main() {
  fun main() {
    val runner = Runner("Kieran")
    val run = RunningExercise(runner)
    run.exercise(10, 45, 10)
  }
}
```

This is demonstrating a key design principle of *encapsulate what varies* what varies here is how a runner exercises. However, what doesn't vary is the order that it is done in. This is defined in our abstract class (the template).

## Conclusion

I find the Template Pattern a good starter for learning about design patterns. It is relatively easy to understand yet provides powerful capabilities.
For learning about Design Patterns the two most important books I've read (and re-read) are the classic GoF book and Head First series on Design Patterns. They also explain further opportunities in the template pattern such as introducing hooks, where you can provide a no-op method that a sub class can optionally override. Thereby allowing you to create clasess that may or may not perform a particular operation.

## References

* [Design Patterns (GOF)](https://www.amazon.com/Design-Patterns-Object-Oriented-Addison-Wesley-Professional-ebook/dp/B000SEIBB8)
* [Head First Design Patterns](https://www.amazon.com/Head-First-Design-Patterns-Object-Oriented-ebook-dp-B08P3X99QP/dp/B08P3X99QP)
* [Why warming up and cooling down is important](https://www.tricitymed.org/2016/12/warming-cooling-important/)
