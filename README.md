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

```scala
package com.rockthejvm.part1as

import scala.util.Try

object DarkSugars {

  // 1 - sugar for methods with one argument
  def singleArgMethod(arg: Int): Int = arg + 1

  val aMethodCall = singleArgMethod({
    // long code
    42
  })

  val aMethodCall_v2 = singleArgMethod {
    // long code
    42
  }

  // example: Try, Future
  val aTryInstance = Try {
    throw new RuntimeException
  }

  // with hofs
  val anIncrementedList = List(1,2,3).map { x =>
    // code block
    x + 1
  }

  // 2 - single abstract method pattern (since Scala 2.12)
  trait Action {
    // can also have other implmented fields/methods here
    def act(x: Int): Int
  }

  val anAction = new Action {
    override def act(x: Int) = x + 1
  }

  val anotherAction: Action = (x: Int) => x + 1 // new Action { def act(x: Int) = x + 1 }

  // example: Runnable
  val aThread = new Thread(new Runnable {
    override def run(): Unit = println("Hi, Scala, from another thread")
  })

  val aSweeterThread = new Thread(() => println("Hi, Scala"))

  // 3 - methods ending in a : are RIGHT-ASSOCIATIVE
  val aList = List(1,2,3)
  val aPrependedList = 0 :: aList // aList.::(0)
  val aBigList = 0 :: 1 :: 2 :: List(3,4) // List(3,4).::(2).::(1).::(0)

  class MyStream[T] {
    infix def -->:(value: T): MyStream[T] = this // impl not important
  }

  val myStream = 1 -->: 2 -->: 3 -->: 4 -->: new MyStream[Int]

  // 4 - multi-word identifiers
  class Talker(name: String) {
    infix def `and then said`(gossip: String) = println(s"$name said $gossip")
  }

  val daniel = new Talker("Daniel")
  val danielsStatement = daniel `and then said` "I love Scala"

  // example: HTTP libraries
  object `Content-Type` {
    val `application/json` = "application/JSON"
  }

  // 5 - infix types
  import scala.annotation.targetName
  @targetName("Arrow") // for more readable bytecode + Java interop
  infix class -->[A, B]
  val compositeType: Int --> String = new -->[Int, String]

  // 6 - update()
  val anArray = Array(1,2,3,4)
  anArray.update(2, 45)
  anArray(2) = 45 // same

  // 7 - mutable fields
  class Mutable {
    private var internalMember: Int = 0
    def member = internalMember // "getter"
    def member_=(value: Int): Unit =
      internalMember = value // "setter"
  }

  val aMutableContainer = new Mutable
  aMutableContainer.member = 42 // aMutableContainer.member_=(42)

  // 8 - variable arguments (varargs)
  def methodWithVarargs(args: Int*) = {
    // return the number of arguments supplied
    args.length
  }

  val callWithZeroArgs = methodWithVarargs()
  val callWithOneArgs = methodWithVarargs(78)
  val callWithTwoArgs = methodWithVarargs(12, 34)

  val aCollection = List(1,2,3,4)
  val callWithDynamicArgs = methodWithVarargs(aCollection*)

  def main(args: Array[String]): Unit = {

  }
}
```

## 3. Advanced Pattern Matching.
## 4. Advanced Pattern Matching, Part 2.

```scala
package com.rockthejvm.part1as

object AdvancedPatternMatching {

  /*
    PM:
    - constants
    - objects
    - wildcards
    - variables
    - infix patterns
    - lists
    - case classes
   */

  class Person(val name: String, val age: Int)

  object Person {
    def unapply(person: Person): Option[(String, Int)] = // person match { case Person(string, int) => }
      if (person.age < 21) None
      else Some((person.name, person.age))

    def unapply(age: Int): Option[String] = // int match { case Person(string) => ... }
      if (age < 21) Some("minor")
      else Some("legally allowed to drink")
  }

  val daniel = new Person("Daniel", 102)
  val danielPM = daniel match { // Person.unapply(daniel) => Option((n, a))
    case Person(n, a) => s"Hi there, I'm $n"
  }

  val danielsLegalStatus = daniel.age match {
    case Person(status) => s"Daniel's legal drinking status is $status"
  }

  // boolean patterns
  object even {
    def unapply(arg: Int): Boolean = arg % 2 == 0
  }

  object singleDigit {
    def unapply(arg: Int): Boolean = arg > -10 && arg < 10
  }

  val n: Int = 43
  val mathProperty = n match {
    case even() => "an even number"
    case singleDigit() => "a one digit number"
    case _ => "no special property"
  }

  // infix patterns
  infix case class Or[A, B](a: A, b: B)
  val anEither = Or(2, "two")
  val humanDescriptionEither = anEither match {
    case number Or string => s"$number is written as $string"
  }

  val aList = List(1,2,3)
  val listPM = aList match {
    case 1 :: rest => "a list starting with 1"
    case _ => "some uninteresting list"
  }

  // decomposing sequences
  val vararg = aList match {
    case List(1, _*) => "list starting with 1"
    case _ => "some other list"
  }

  abstract class MyList[A] {
    def head: A = throw new NoSuchElementException
    def tail: MyList[A] = throw new NoSuchElementException
  }
  case class Empty[A]() extends MyList[A]
  case class Cons[A](override val head: A, override val tail: MyList[A]) extends MyList[A]

  object MyList {
    def unapplySeq[A](list: MyList[A]): Option[Seq[A]] =
      if (list == Empty()) Some(Seq.empty)
      else unapplySeq(list.tail).map(restOfSequence => list.head +: restOfSequence)
  }

  val myList: MyList[Int] = Cons(1, Cons(2, Cons(3, Empty())))
  val varargCustom = myList match {
    case MyList(1, _*) => "list starting with 1"
    case _ => "some other list"
  }

  // custom return type for unapply
  abstract class Wrapper[T] {
    def isEmpty: Boolean
    def get: T
  }

  object PersonWrapper {
    def unapply(person: Person): Wrapper[String] = new Wrapper[String] {
      override def isEmpty = false
      override def get = person.name
    }
  }

  val weirdPersonPM = daniel match {
    case PersonWrapper(name) => s"Hey my name is $name"
  }

  def main(args: Array[String]): Unit = {
    println(danielPM)
    println(danielsLegalStatus)
    println(mathProperty)
  }
}
```

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




