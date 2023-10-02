# Udemy Advanced Scala and Functional Programming Rock the JVM

https://github.com/rockthejvm/scala-3-advanced

https://www.udemy.com/course/advanced-scala

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

```scala
package com.rockthejvm.part2afp

object PartialFunctions {

  val aFunction: Int => Int = x => x + 1

  val aFussyFunction = (x: Int) =>
    if (x == 1) 42
    else if (x == 2) 56
    else if (x == 5) 999
    else throw new RuntimeException("no suitable cases possible")

  val aFussyFunction_v2 = (x: Int) => x match {
    case 1 => 42
    case 2 => 56
    case 5 => 999
  }

  // partial function
  val aPartialFunction: PartialFunction[Int, Int] = { // x => x match { ... }
    case 1 => 42
    case 2 => 56
    case 5 => 999
  }

  // utilities on PFs
  val canCallOn37 = aPartialFunction.isDefinedAt(37)
  val liftedPF = aPartialFunction.lift // Int => Option[Int]

  val anotherPF: PartialFunction[Int, Int] = {
    case 45 => 86
  }
  val pfChain = aPartialFunction.orElse[Int, Int](anotherPF)

  // HOFs accept PFs as arguments
  val aList = List(1,2,3,4)
  val aChangedList = aList.map(x => x match {
    case 1 => 4
    case 2 => 3
    case 3 => 45
    case 4 => 67
    case _ => 0
  })

  val aChangedList_v2 = aList.map({ // possible because PartialFunction[A, B] extends Function1[A, B]
    case 1 => 4
    case 2 => 3
    case 3 => 45
    case 4 => 67
    case _ => 0
  })

  val aChangedList_v3 = aList.map {
    case 1 => 4
    case 2 => 3
    case 3 => 45
    case 4 => 67
    case _ => 0
  }

  case class Person(name: String, age: Int)
  val someKids = List(
    Person("Alice", 3),
    Person("Bobbie", 5),
    Person("Jane", 4)
  )

  val kidsGrowingUp = someKids.map {
    case Person(name, age) => Person(name, age + 1)
  }


  def main(args: Array[String]): Unit = {
    println(aPartialFunction(2))
    // println(aPartialFunction(33)) // throws MatchError
    println(liftedPF(5)) // Some(999)
    println(liftedPF(37)) // None
    println(pfChain(45))
  }
}
```

## 6. Functional Collections: a functional Set.

```scala
package com.rockthejvm.practice

import scala.annotation.tailrec


abstract class FSet[A] extends (A => Boolean) {
  // main api
  def contains(elem: A): Boolean
  def apply(elem: A): Boolean = contains(elem)

  infix def +(elem: A): FSet[A]
  infix def ++(anotherSet: FSet[A]): FSet[A]

  // "classics"
  def map[B](f: A => B): FSet[B]
  def flatMap[B](f: A => FSet[B]): FSet[B]
  def filter(predicate: A => Boolean): FSet[A]
  def foreach(f: A => Unit): Unit

  // utilities
  infix def -(elem: A): FSet[A]
  infix def --(anotherSet: FSet[A]): FSet[A]
  infix def &(anotherSet: FSet[A]): FSet[A]

  // "negation" == all the elements of type A EXCEPT the elements in this set
  def unary_! : FSet[A] = new PBSet(x => !contains(x))
}

// example { x in N | x % 2 == 0 }
// property-based set
class PBSet[A](property: A => Boolean) extends FSet[A] {
  // main api
  def contains(elem: A): Boolean = property(elem)

  infix def +(elem: A): FSet[A] =
    new PBSet(x => x == elem || property(x))
  infix def ++(anotherSet: FSet[A]): FSet[A] =
    new PBSet(x => property(x) || anotherSet(x))

  // "classics"
  def map[B](f: A => B): FSet[B] =
    politelyFail()
  def flatMap[B](f: A => FSet[B]): FSet[B] =
    politelyFail()
  def filter(predicate: A => Boolean): FSet[A] =
    new PBSet(x => property(x) && predicate(x))
  def foreach(f: A => Unit): Unit =
    politelyFail()

  // utilities
  infix def -(elem: A): FSet[A] =
    filter(x => x != elem)
  infix def --(anotherSet: FSet[A]): FSet[A] =
    filter(!anotherSet)
  infix def &(anotherSet: FSet[A]): FSet[A] =
    filter(anotherSet)

  // extra utilities (internal)
  private def politelyFail() = throw new RuntimeException("I don't know if this set is iterable...")
}

case class Empty[A]() extends FSet[A] { // PBSet(x => false)
  override def contains(elem: A) = false
  infix def +(elem: A): FSet[A] = Cons(elem, this)
  infix def ++(anotherSet: FSet[A]): FSet[A] = anotherSet

  // "classics"
  def map[B](f: A => B): FSet[B] = Empty()
  def flatMap[B](f: A => FSet[B]): FSet[B] = Empty()
  def filter(predicate: A => Boolean): FSet[A] = this
  def foreach(f: A => Unit): Unit = ()

  // utilities
  infix def -(elem: A): FSet[A] = this
  infix def --(anotherSet: FSet[A]): FSet[A] = this
  infix def &(anotherSet: FSet[A]): FSet[A] = this

}

case class Cons[A](head: A, tail: FSet[A]) extends FSet[A] {
  override def contains(elem: A) = elem == head || tail.contains(elem)
  infix def +(elem: A): FSet[A] =
    if (contains(elem)) this
    else Cons(elem, this)

  infix def ++(anotherSet: FSet[A]): FSet[A] = tail ++ anotherSet + head

  // "classics"
  def map[B](f: A => B): FSet[B] = tail.map(f) + f(head)
  def flatMap[B](f: A => FSet[B]): FSet[B] = tail.flatMap(f) ++ f(head)
  def filter(predicate: A => Boolean): FSet[A] = {
    val filteredTail = tail.filter(predicate)
    if predicate(head) then filteredTail + head
    else filteredTail
  }

  def foreach(f: A => Unit): Unit = {
    f(head)
    tail.foreach(f)
  }

  // utilities
  infix def -(elem: A): FSet[A] =
    if (head == elem) tail
    else tail - elem + head

  infix def --(anotherSet: FSet[A]): FSet[A] = filter(!anotherSet)
  infix def &(anotherSet: FSet[A]): FSet[A] = filter(anotherSet) // intersection = filtering
}

object FSet {
  def apply[A](values: A*): FSet[A] = {
    @tailrec
    def buildSet(valuesSeq: Seq[A], acc: FSet[A]): FSet[A] =
      if (valuesSeq.isEmpty) acc
      else buildSet(valuesSeq.tail, acc + valuesSeq.head)

    buildSet(values, Empty())
  }
}

object FunctionalSetPlayground {

  def main(args: Array[String]): Unit = {

    val first5 = FSet(1,2,3,4,5)
    val someNumbers = FSet(4,5,6,7,8)
    println(first5.contains(5)) // true
    println(first5(6))          // false
    println((first5 + 10).contains(10)) // true
    println(first5.map(_ * 2).contains(10)) // true
    println(first5.map(_ % 2).contains(1))  // true
    println(first5.flatMap(x => FSet(x, x + 1)).contains(7)) // false

    println((first5 - 3).contains(3)) // false
    println((first5 -- someNumbers).contains(4)) // false
    println((first5 & someNumbers).contains(4)) // true

    val naturals = new PBSet[Int](_ => true)
    println(naturals.contains(5237548)) // true
    println(!naturals.contains(0)) // false
    println((!naturals + 1 + 2 + 3).contains(3)) // true
    // println(!naturals.map(_ + 1)) // throws - map/flatMap/foreach will not work
  }
}
```


## 7. Enhancing a functional Set.

## 8. A Functional Set, level 9000: a Potentially Infinite Set.

## 9. Moar Functional Collections.

## 10. Currying and Partially Applied.

## 11. Lazy Evaluation.

```scala
package com.rockthejvm.part2afp

object LazyEvaluation {

  lazy val x: Int = {
    println("Hello")
    42
  }

  // lazy DELAYS the evaluation of a value until the first use
  // evaluation occurs ONCE

  /*
    Example 1: call by need
   */
  def byNameMethod(n: => Int): Int =
    n + n + n + 1

  def retrieveMagicValue() = {
    println("waiting...")
    Thread.sleep(1000)
    42
  }

  def demoByName(): Unit = {
    println(byNameMethod(retrieveMagicValue()))
    // retrieveMagicValue() + retrieveMagicValue() + retrieveMagicValue() + 1
  }

  // call by need = call by name + lazy values
  def byNeedMethod(n: => Int): Int = {
    lazy val lazyN = n // memoization
    lazyN + lazyN + lazyN + 1
  }

  def demoByNeed(): Unit = {
    println(byNeedMethod(retrieveMagicValue()))
  }

  /*
    Example 2: withFilter
   */
  def lessThan30(i: Int): Boolean = {
    println(s"$i is less than 30?")
    i < 30
  }

  def greaterThan20(i: Int): Boolean = {
    println(s"$i is greater than 20?")
    i > 20
  }

  val numbers = List(1, 25, 40, 5, 23)

  def demoFilter(): Unit = {
    val lt30 = numbers.filter(lessThan30)
    val gt20 = lt30.filter(greaterThan20)
    println(gt20)
  }

  def demoWithFilter(): Unit = {
    val lt30 = numbers.withFilter(lessThan30)
    val gt20 = lt30.withFilter(greaterThan20)
    println(gt20.map(identity))
  }

  def demoForComprehension(): Unit = {
    val forComp = for {
      n <- numbers if lessThan30(n) && greaterThan20(n)
    } yield n
    println(forComp)
  }

  def main(args: Array[String]): Unit = {
    demoForComprehension()
  }
}
```

## 12. Lazy Evaluation: exercise (a potentially infinite stream)

## 13. Infinite Streams proficiency: exercise

## 14. Monads.

```scala
package com.rockthejvm.part2afp

import scala.annotation.targetName

object Monads {

  def listStory(): Unit = {
    val aList = List(1,2,3)
    val listMultiply = for {
      x <- List(1,2,3)
      y <- List(4,5,6)
    } yield x * y
    // for comprehensions = chains of map + flatMap
    val listMultiply_v2 = List(1,2,3).flatMap(x => List(4,5,6).map(y => x * y))

    val f = (x: Int) => List(x, x + 1)
    val g = (x: Int) => List(x, 2 * x)
    val pure = (x: Int) => List(x) // same as the list "constructor"

    // prop 1: left identity
    val leftIdentity = pure(42).flatMap(f) == f(42) // for every x, for every f

    // prop 2: right identity
    val rightIdentity = aList.flatMap(pure) == aList // for every list

    // prop 3: associativity
    /*
      [1,2,3].flatMap(x => [x, x+1]) = [1,2,2,3,3,4]
      [1,2,2,3,3,4].flatMap(x => [x, 2*x]) = [1,2, 2,4,    2,4, 3,6,     3,6, 4,8]
      [1,2,3].flatMap(f).flatMap(g) = [1,2, 2,4, 2,4, 3,6, 3,6, 4,8]

      [1,2,2,4] = f(1).flatMap(g)
      [2,4,3,6] = f(2).flatMap(g)
      [3,6,4,8] = f(3).flatMap(g)
      [1,2, 2,4, 2,4, 3,6, 3,6, 4,8] = f(1).flatMap(g) ++ f(2).flatMap(g) ++ f(3).flatMap(g)
      [1,2,3].flatMap(x => f(x).flatMap(g))
     */
    val associativity = aList.flatMap(f).flatMap(g) == aList.flatMap(x => f(x).flatMap(g))
  }

  def optionStory(): Unit = {
    val anOption = Option(42)
    val optionString = for {
      lang <- Option("Scala")
      ver <- Option(3)
    } yield s"$lang-$ver"
    // identical
    val optionString_v2 = Option("Scala").flatMap(lang => Option(3).map(ver => s"$lang-$ver"))

    val f = (x: Int) => Option(x + 1)
    val g = (x: Int) => Option(2 * x)
    val pure = (x: Int) => Option(x) // same as Option "constructor"

    // prop 1: left-identity
    val leftIdentity = pure(42).flatMap(f) == f(42) // for any x, for any f

    // prop 2: right-identity
    val rightIdentity = anOption.flatMap(pure) == anOption // for any Option

    // prop 3: associativity
    /*
      anOption.flatMap(f).flatMap(g) = Option(42).flatMap(x => Option(x + 1)).flatMap(x => Option(2 * x)
      = Option(43).flatMap(x => Option(2 * x)
      = Option(86)

      anOption.flatMap(x => f(x).flatMap(g)) = Option(42).flatMap(x => Option(x + 1).flatMap(y => 2 * y)))
      = Option(42).flatMap(x => 2 * x + 2)
      = Option(86)
     */
    val associativity = anOption.flatMap(f).flatMap(g) == anOption.flatMap(x => f(x).flatMap(g)) // for any option, f and g
  }

  // MONADS = chain dependent computations

  // exercise: IS THIS A MONAD?
  // answer: IT IS A MONAD!
  // interpretation: ANY computation that might perform side effects
  case class IO[A](unsafeRun: () => A) {
    def map[B](f: A => B): IO[B] =
      IO(() => f(unsafeRun()))

    def flatMap[B](f: A => IO[B]): IO[B] =
      IO(() => f(unsafeRun()).unsafeRun())
  }

  object IO {
    @targetName("pure")
    def apply[A](value: => A): IO[A] =
      new IO(() => value)
  }

  def possiblyMonadStory(): Unit = {
    val aPossiblyMonad = IO(42)
    val f = (x: Int) => IO(x + 1)
    val g = (x: Int) => IO(2 * x)
    val pure = (x: Int) => IO(x)

    // prop 1: left-identity
    val leftIdentity = pure(42).flatMap(f) == f(42)

    // prop 2: right-identity
    val rightIdentity = aPossiblyMonad.flatMap(pure) == aPossiblyMonad

    // prop 3: associativity
    val associativity = aPossiblyMonad.flatMap(f).flatMap(g) == aPossiblyMonad.flatMap(x => f(x).flatMap(g))

    println(leftIdentity)
    println(rightIdentity)
    println(associativity)
    println(IO(3) == IO(3))
    // ^^ false negative.

    // real tests: values produced + side effect ordering
    val leftIdentity_v2 = pure(42).flatMap(f).unsafeRun() == f(42).unsafeRun()
    val rightIdentity_v2 = aPossiblyMonad.flatMap(pure).unsafeRun() == aPossiblyMonad.unsafeRun()
    val associativity_v2 = aPossiblyMonad.flatMap(f).flatMap(g).unsafeRun() == aPossiblyMonad.flatMap(x => f(x).flatMap(g)).unsafeRun()

    println(leftIdentity_v2)
    println(rightIdentity_v2)
    println(associativity_v2)

    val fs = (x: Int) => IO {
      println("incrementing")
      x + 1
    }

    val gs = (x: Int) => IO {
      println("doubling")
      x * 2
    }

    val associativity_v3 = aPossiblyMonad.flatMap(fs).flatMap(gs).unsafeRun() == aPossiblyMonad.flatMap(x => fs(x).flatMap(gs)).unsafeRun()
  }

  def possiblyMonadExample(): Unit = {
    val aPossiblyMonad = IO {
      println("printing my first possibly monad")
      // do some computations
      42
    }

    val anotherPM = IO {
      println("my second PM")
      "Scala"
    }

    val aForComprehension = for { // computations are DESCRIBED, not EXECUTED
      num <- aPossiblyMonad
      lang <- anotherPM
    } yield s"$num-$lang"
  }

  def main(args: Array[String]): Unit = {
    possiblyMonadStory()
  }
}
```

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




