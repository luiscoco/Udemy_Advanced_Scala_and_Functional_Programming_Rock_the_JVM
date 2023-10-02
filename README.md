# Udemy Advanced Scala and Functional Programming Rock the JVM

https://github.com/rockthejvm/scala-3-advanced

## 1. Recap: the Scala basics.

```scala
package com.rockthejvm.part1as

import scala.annotation.tailrec

object Recap {

  // values, types, expressions
  val aCondition = false // vals are constants
  val anIfExpression = if (aCondition) 42 else 55 // expressions evaluate to a value

  val aCodeBlock = {
    if (aCondition) 54
    78
  }

  // types: Int, String, Double, Boolean, Char, ...
  // Unit = () == "void" in other languages
  val theUnit = println("Hello, Scala")

  // functions
  def aFunction(x: Int): Int = x + 1

  // recursion: stack & tail
  @tailrec def factorial(n: Int, acc: Int): Int =
    if (n <= 0) acc
    else factorial(n - 1, n * acc)

  val fact10 = factorial(10, 1)

  // object oriented programming
  class Animal
  class Dog extends Animal
  val aDog: Animal = new Dog

  trait Carnivore {
    infix def eat(a: Animal): Unit
  }

  class Crocodile extends Animal with Carnivore {
    override infix def eat(a: Animal): Unit = println("I'm a croc, I eat everything")
  }

  // method notation
  val aCroc = new Crocodile
  aCroc.eat(aDog)
  aCroc eat aDog // "operator"/infix position

  // anonymous classes
  val aCarnivore = new Carnivore {
    override infix def eat(a: Animal): Unit = println("I'm a carnivore")
  }

  // generics
  abstract class LList[A] {
    // type A is known inside the implementation
  }
  // singletons and companions
  object LList // companion object, used for instance-independent ("static") fields/methods

  // case classes
  case class Person(name: String, age: Int)

  // enums
  enum BasicColors {
    case RED, GREEN, BLUE
  }

  // exceptions and try/catch/finally
  def throwSomeException(): Int =
    throw new RuntimeException

  val aPotentialFailure = try {
    // code that might fail
    throwSomeException()
  } catch {
    case e: Exception => "I caught an exception"
  } finally {
    // closing resources
    println("some important logs")
  }

  // functional programming
  val incrementer = new Function1[Int, Int] {
    override def apply(x: Int) = x + 1
  }

  val two = incrementer(1)

  // lambdas
  val anonymousIncrementer = (x: Int) => x + 1
  // hofs = higher-order functions
  val anIncrementerList = List(1,2,3).map(anonymousIncrementer) // [2,3,4]
  // map, flatMap, filter

  // for-comprehensions
  val pairs = for {
    number <- List(1,2,3)
    char <- List('a', 'b')
  } yield s"$number-$char"

  // Scala collections: Seqs, Arrays, Lists, Vectors, Maps, Tupes, Sets

  // options, try
  val anOption: Option[Int] = Option(42)

  // pattern matching
  val x = 2
  val order = x match {
    case 1 => "first"
    case 2 => "second"
    case _ => "not important"
  }

  val bob = Person("Bob", 22)
  val greeting = bob match {
    case Person(n, _) => s"Hi, my name is $n"
  }

  // braceless syntax
  val pairs_v2 =
    for
      number <- List(1,2,3)
      char <- List('a', 'b')
    yield s"$number-$char"
    // same for if, match, while

  // indentation tokens
  class BracelessAnimal:
    def eat(): Unit =
      println("I'm doing something")
      println("I'm eating")
    end eat
  end BracelessAnimal

  def main(args: Array[String]): Unit = {

  }
}
```

## 2. Dark Syntax sugar.

## 3. Advanced Pattern Matching.

## 4. Advanced Pattern Matching, Part 2.

# Advanced Functional Programming

## 5. Partial Functions.

## 6. Functional Collections: a functional Set.

## 7. Enhancing a functional Set.

## 8. A Functional Set, level 9000: a Potentially Infinite Set.

## 9. Moar Functional Collections.

## 10. Currying and Partially Applied.

## 11. Lazy Evaluation.

## 12. Lazy Evaluation: exercise (a potentially infinite stream)

## 13. Infinite Streams proficiency: exercise

## 14. Monads.

## 15. Monads: exercises.

# Functional Concurrent Programming

## 16. Intro to Parallel Programming on the JVM.

## 17. Concurrency problems on the JVM.

## 18. JVM Thread Communication.

## 19. Producer-Consumer, Level 2.

## 20. Producer-Consumer, Level 3+, exercises.

## 21. JVM Thread Communication. Exercises.
   
## 22. Futures and Promises.

## 23. Futures, Part 2.

## 24. Futures, Part 3.

## 25. Futures, Part 4 + Exercises.

# Implicits

## 26. Enter Implicits.

## 27. Organizing Implicits.

## 28. Type Classes, Part 1.

## 29. Type Classes, Part 2.

## 30. Pimp My Library!.

## 31. Type Classes, Part 3.

## 32. A Type Class End-to-End example: JSON Serialization.

## 33. A Type Class Use Case: The Magnet Pattern.

## 34. Scala 3: Given Instances and Using Clauses.

## 35. Scala 3: Extension Methods.

# Mastering the Type System

## 36. Advanced Inheritance.

## 37. Variance.

## 38. Variance: exercises.

## 39. Type Members.

## 40. Inner Types and Path-Dependent Types.

## 41. Self Types.

## 42. Recursive Types and F-Bounded Polymorphism.

## 43. Higher-Kinded Types.




