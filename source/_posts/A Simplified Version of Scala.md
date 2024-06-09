---
title: "Kotlin: A Simplified Version of Scala"
date: 2019-07-06 10:18:22
tags: Programming Languages
---

A swordsman in the martial world must have a handy sword. A good programming language is like a good sword: the weight is appropriate, it feels comfortable, it's elegant to wield, and its lethality is adequate. The official stance on Kotlin's relationship with Scala is, "If you're happy with Scala, you don't need Kotlin."

### Scripting

Like Java, Scala's basic unit of execution is a class, while Kotlin allows the main method in a file to run directly without requiring a class. Java's entry function is defined within a class:

```Java
public class Java {
    public static void main(String[] args) {}
}
```

Scala's entry function is defined within an object rather than a regular class:

```Scala
object Scala {
  def main(args: Array[String]): Unit = {}
}
```

Kotlin's entry function is defined directly in a .kt file; thus, a class in Kotlin is merely a data structure where an entry function cannot be defined:

```Kotlin
fun main(args: Array<String>) {}
```

### Constructors and Singleton Pattern

Kotlin's constructor, like Scala's, is written in the class definition, and thus cannot include initialization code directly as Java's constructors can. In Kotlin, the init block is used to execute initialization code:

```Kotlin
class Test(arg: String) {
    init {
        println("This string is ${arg}")
    }
}

fun main(args: Array<String>) {
    val test = Test("smallyu")
}

// This string is smallyu
```

If a second constructor is needed, you have to use a constructor function similar to ES6, or a secondary constructor like in Scala. This is indeed an awkward way of writing, making Java seem more user-friendly in comparison.

```Kotlin
class Test(arg1: String) {
    init {
        println("This string is ${arg1}")
    }
    constructor(arg2: Int): this("smallyu2") {
        println("This int is ${arg2}")
    }
}

fun main(args: Array<String>) {
    val test = Test(1)
}

// This string is smallyu2
// This int is 1
```

Kotlin's constructor needs to be defined with the constructor keyword, which can be omitted by default, but if you want to add access modifiers, you naturally can't omit it. Implementing the singleton pattern in Kotlin is similar to Java: make the constructor private, and expose the instance through a static method:

```Kotlin
class Test private constructor() {
    companion object Factory {
        fun create(): Test = Test()
    }
}

fun main(args: Array<String>) {
    val test = Test.Factory.create()
}
```

In Kotlin, the object keyword defines a static block, and companion allows defining static blocks within the class, hence the companion object defines a method create() that can be accessed externally.

### Getter and Setter

Another interesting feature in Kotlin is the getter and setter. The principle behind data binding in frontend frameworks like React or Vue is using Object.defineProperty() to define an object's getter and setter, allowing changes to the object to be synchronized with the page in real-time. Kotlin provides support for property getters and setters:

```Kotlin
var test: Int
    get() {
        println("There is test getter")
        return 2
    }
    set(arg) {
        println("The setter arg is ${arg}")
    }

fun main(args: Array<String>) {
    println(test)
    test = 3
}

// There is test getter
// 2
// The setter arg is 3
```

### Others

I became interested in Kotlin because I found that Kotlin supports coroutines. If Kotlin truly has language-level coroutine support, combined with its ability to run on the JVM and develop multi-platform applications including server-side, Android, JavaScript, and Native, Kotlin would undoubtedly be an extremely powerful programming language. However, Kotlin's coroutines are just an extension package and still require a compiler tool to introduce, so Go language remains dominant in coroutine support. For the JavaScript platform, Kotlin is not as easy to use as TypeScript. As for Android and Native, they are already Java's application scenarios...

Kotlin provides a lot of syntactic sugar, seemingly reducing the amount of code a programmer has to write, but to proficiently apply Kotlin's features, users must understand concepts similar to data classes, just like Scala's case classes. Kotlin is less academic than Scala and does not have a significant advantage over Java in terms of engineering capabilities. Although Go language takes a different path and has widely criticized language features, it is satisfying to read and write. Therefore, like Scala, Kotlin will not have a broad application prospect. In other words, it will not be the next popular programming language.
