---
title: Basics of Scala Syntax
date: 2018-12-17 11:06:03
tags: Programming Language
---

The syntax of Scala is relatively complex. In the spirit of incremental software development, learning a programming language should start with finding an expressive way that one can master and then gradually build upon. Scala supports both object-oriented and functional programming, which is one reason for its complex syntax. Some tutorials are very comprehensive, but this comprehensiveness can make it difficult to distill the main points.

The following content focuses on the simplest basic syntax, hoping that based on this content, one can try to write object-oriented style Scala code.

### Statements

Scala allows statements to end without a `;`, similar to JavaScript.

### Variable Definition

`val` defines immutable variables (constants), `var` defines mutable variables:

```Scala
val msg1 = "Hello World"
var msg2 = "Hello Wrold"

val msg3: String = "Hello World"
```

When defining variables, type declarations are on the right side of the variable and are optional; they can be omitted as the compiler will automatically infer the type. Basic types in Scala include:

> Byte, Short, Int, Long, Char, String, Float, Double, Boolean

### Function Definition

Functions are methods. Below is an example of defining a function:

```Scala
def max(x: Int, y: Int): Int = {
  if (x > y) {
    return x
  } else { 
    return y
  }
}
```

The significant differences from Java in method definitions are: first, using the `def` keyword to define functions; second, type declarations are on the right side of the variable; third, an `=` connects the function declaration and the function body.

Note that function parameters must have explicitly defined types; the compiler cannot automatically infer parameter types. The return type is optional unless the function uses recursion. Additionally, the `return` keyword is also optional; if there is no explicit return statement, the program will return the last computed result.

If the statement after `if` is a single statement, braces can be omitted. Therefore, the function can also be written like this:

```Scala
def max2(x: Int, y: Int) = if (x > y) x else y
```

### Conditional Structures

The above example has already used an `if` statement. Scala's `if` statement is not special, but compared to other languages, Scala uses pattern matching to replace the traditional `switch` structure:

```Scala
val a = 1

a match {
  case 1 => println(1)
  case 2 => println(2)
  case _ =>
}
```

The `_` wildcard matches all values and is used to capture the default case. In match expressions, cases never fall through to the next case, so no `break` or `return` is needed (if `_` is placed first, the program will not continue executing). However, be careful, if the program does not match any case, it will throw a `MatchError`.

### Loop Structures

The `while` loop is not the recommended style of code in Scala:

```Scala
var i = 0
while (i < 5) {
  println(i)
  i += 1
}
```

This is a typical `while` loop. Compared to imperative languages, Scala does not have the `++` operator; one must use statements like `i += 1`.

Mentioning `while` will inevitably bring up `for`. The `for` loop in Scala has some differences from imperative languages. In the following example, the program will print from 0 to 4:

```Scala
for (i <- 0 until 5) {
  println(i)
}
```

Scala does not recommend `while` loops and prefers a functional programming style. The `foreach` method is one of them:

```Scala
"abc".foreach(c => println(c))
```

The program will print the characters `a`, `b`, `c` each on a new line. If the function body has only one statement and one parameter, the code can be more concise:

```Scala
"abc".foreach(println)
```

### Arrays

Scala's arrays are not implemented at the language level and can be used by instantiating the `Array` class. Correspondingly, array subscripts are represented using parentheses (i.e., method parameters):

```Scala
val greet  = new Array 

greet(0) = "a"
greet(1) = "b"
greet(2) = "c"

greet.foreach(println)
```

When instantiating objects, default parameters can be directly passed. Array is indeed just a regular class; the following way of writing does not involve any black magic, it just uses a case class, which will be mentioned later.

```Scala
val greet2 = Array("a", "b", "c")
greet2.foreach(println)
```

### Classes

Classes are defined using the `class` keyword, and classes contain fields and methods, i.e., typical object-oriented features. Unlike Python, Scala still supports access control:

```Scala
class Accumulator {
  private var sum = 0
  def add(b: Byte): Unit = {
    sum += b
    println(sum)
  }
}
```

### Singleton Objects

Singleton objects are equivalent to static classes in Java and are defined using the `object` keyword instead of `class`. Singleton objects are shared by the program and can be called directly. Singleton objects can serve as the entry point of the program by defining the `main` method within the singleton object. The following program instantiates an object `a` from the previously defined `Accumulator` class and calls its `add` method, ultimately printing `1`:

```Scala
object Run {
  def main(args: Array[String]): Unit = {
    val a = new Accumulator
    a.add(1)
  }
}
```

In the same source file, when a singleton object and a class have the same name, the singleton object is called the companion object of the class, and the class is the companion class of the singleton object. A class can access the private properties and methods of its companion object.

### Constructors

Scala's rules for constructors are stricter than Java's. Scala implements constructors through the concept of class parameters:

```Scala
class Accumulator(a: Int, b: Int)
```

If the class has no body, braces can be omitted. When instantiating this class, parameters need to be passed. In Java, overloaded constructors correspond to auxiliary constructors in Scala, which look like this:

```Scala
class Accumulator(a: Int, b: Int) {
  def this(c: Int) = this(c, 1)
}
```

This class now has two constructors:

```Scala
val a1 = new Accumulator(1)
val a2 = new Accumulator(1, 2)
```

The strictness of Scala constructors lies in the fact that the second constructor can only leverage the first constructor or the superclass constructor.

### Inheritance and Overriding

Scala's inheritance is not significantly different from Java's, except that method overriding must use the `override` keyword:

```Scala
class A(a: Int) {
  def test = println("a")
}

class B(b: Int) extends A(b) {
  override def test = println("b")
}
```

### Traits

Traits are similar to singleton objects, except for the keyword used during definition. Traits, like regular classes, can contain fields and methods. The significance of traits lies in supporting mixins and allowing multiple traits to be mixed in. This feature is often compared with multiple inheritance.

```Scala
trait A {
  def aMethod = println("A")
}

trait B {
  def bMethod = println("B")
}

class C extends A with B
```

This way, an instance of `C` can call `aMethod` and `bMethod`:

```Scala
val c = new C
c.aMethod
c.bMethod
```

### Case Classes

Case classes are defined by adding the `case` keyword before the `class`. This modifier allows the Scala compiler to automatically add some convenient settings to the class: 1. Instances can be created without the `new` keyword; 2. Parameters are automatically treated as class fields; 3. The class automatically has `toString`, `hashCode`, and `equals` methods:

```Scala
case class A(a: Int) {
  def aMethod = println(a)
}

object Run {
  def main(args: Array[String]): Unit = {
    val a = A(1)
    a.aMethod     // 1
    println(a)    // A(1)
    println(a.a)  // 1
  }
}
```

### Other

Compared to Java, Scala supports abstract classes but not interfaces. Abstract classes are defined using `abstract`, and interfaces are replaced by traits. Scala also supports generics, annotations, and other syntax.

### Follow-Up

The above content is not comprehensive and may not be sufficient. To use a programming language, in addition to mastering its basic syntax, one should also be familiar with its idiomatic usage, especially for multi-paradigm languages like Scala. This content will be continuously updated and improved, and other features of Scala will be discussed further.
