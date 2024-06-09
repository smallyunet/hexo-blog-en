---
title: Basic Syntax of Go Language
date: 2019-03-15 21:18:11
tags: Programming Language
---

Although Go language is not highly regarded in terms of language design by Wang Yin, its concise code structure is indeed captivating.

### Statements

Go language statements do not require a `;` at the end.

### Variables and Constants

Use `var` to declare variables. When variables need to be initialized, you can use the assignment symbol `:=` instead of `=` to omit the `var` keyword.

```go
var a int
var b string

var c int = 10
var d = "golang"  // The compiler automatically infers the type
d := 10
```

Unlike C or Java, Go language's type declaration is on the right side of the variable. Note that if declared variables in the program are not used, the program will not compile. Go is an engineering-oriented language, so some of its features might seem unreasonable but will improve efficiency in actual engineering.

Go's variable assignment supports some cool writing styles, such as swapping the values of variables x and y using this counter-intuitive method:

```go
x, y = y, x
```

In Go, `const` is used to define constants. `true`, `false`, and `iota` are predefined constants. Among them, `iota` is a bit special. `iota` resets to 0 each time the `const` keyword appears, and before the next `const` appears, the value of `iota` increments by 1 each time it appears.

```go
const a = iota   // 0
const b = iota   // 0
const (
  c = iota       // 0
  d = iota       // 1
)
```

### Arrays and Slices

Declare an array of 3 elements and initialize it:

```go
array := [3]int{0, 1, 2}
array[0] = 3
fmt.Println(array)
```

Like other languages, Go cannot change the size of an array after declaration. Therefore, Go provides slices similar to Python. Slices can be created from arrays or using the `make()` function.

```go
array := [3]int{0, 1, 2}
slice1 := array[:2]       // Created from an array

slice2 := make([]int, 3)  // Created directly

fmt.Println(slice1)       // [0 1]
fmt.Println(slice2)       // [0 0 0]
```

In addition to slices, maps are also created using the `make` function. The full type declaration of a map is `var myMap map[string]int`, meaning the variable `myMap` has a key of type `string` and a value of type `int`.

### Control Flow

Go allows `if-else` statements without parentheses around the condition, although adding them is also fine.

```go
a := 1
if a == 1 {
  print(1)
} else if (a == 2) {
  print(2)
} else {
  print(3)
}
```

`switch` statements do not require parentheses around the condition, nor do they require a `break`. Other matches will not execute, similar to Scala. Optimizing `switch` statements seems to be a common practice.

```go
i := 0
switch i {
case 0:
  print(0)
case 1:
  print(1)
}
```

Loop conditions also do not require parentheses. Go only supports `for` loops and has optimized for infinite loops, no longer requiring the `for(;;)` syntax.

```go
for {
  print(1)
}
```

### Functions

Go language originates from the C language faction, so from the beginning, Go is not an OOP or FP language and does not have the concept of classes or objects. Functions are first-class citizens in the program. Like in C, the `main` function (under the `main` package) is the entry point of the entire program.

```go
func add(a int, b int) (int, int) {
  return a + b, a - b
}

func main() {
  x, y := add(1, 2)
  print(x, y)
}
```

Go statements are concise and efficient, with the first parenthesis after the function name for parameters and the second for return values. Functions support multiple return values. If parameter types are the same, their type declarations can be combined, such as `(a, b int)`.

### Structs

Although Go does not have classes or objects, it does have powerful structs similar to those in C. Here, we define a `Person` struct with two properties `name` and `age`, and add a method `getInfo` to `Person` to output information about a `Person` object:

```go
type Person struct {
  name string
  age int
}

func (p Person) getInfo() {
  print(p.name, p.age)
}

func main() {
  smallyu := new(Person)
  smallyu.name = "smallyu"
  smallyu.age = 1
  smallyu.getInfo()
}
```

Understanding such a program with OOP thinking is not inappropriate. In addition to structs, Go also retains the concept of pointers. Java programmers might be a bit unfamiliar with pointers. Regarding the application of pointers in struct methods, a simple example can help understand:

```go
type Person struct {
  name string
}

func (p Person) setName() {
  p.name = "set name"
}

func (p *Person) setName2() {
  p.name = "set name"
}

func main() {
  smallyu := &Person{"smallyu"}
  smallyu.setName()
  fmt.Println(smallyu)        // &{smallyu}

  bigyu := &Person{"bigyu"}
  bigyu.setName2()
  fmt.Println(bigyu)          // &{set name}
}
```

Methods defined with value types have parameters as formal parameters; methods defined with reference types have parameters as actual parameters. `&{}` is one way to initialize objects, equivalent to `new()`.

### Anonymous Composition

The concept of anonymous composition in Go is equivalent to inheritance in OOP languages. A struct can inherit the properties and methods of another struct, roughly like this.

```go
type Father struct {
  name string
}

func (f Father) getName() {
  print(f.name)
}

type Son struct {
  Father
}

func main() {
  smallyu := &Son{}
  smallyu.name = "smallyu"
  smallyu.getName()       // smallyu
}
```

`Son` does not define the `name` property or the `getName()` method; they are inherited from `Father`.

### Interfaces

Go's interfaces are non-intrusive. As long as a struct implements all the methods in an interface, the program will consider the struct to have implemented that interface.

```go
type IPerson interface {
  getName()
}

type Person struct {
  name string
}

func (p Person) getName() {
  print(p.name)
}

func main() {
  var smallyu IPerson = &Person{"smallyu"}
  smallyu.getName()
}
```

### Goroutines

The keyword for using goroutines is `go`. The naming itself reflects the importance of goroutines to Go. Goroutines are lightweight threads, and starting a goroutine is very simple:

```go
func f(msg string) {
  println(msg)
}

func main() {
  f("direct call")
  go f("goroutine call")
}
```

Running the program, you will find it only prints "direct call". Does this seem familiar? `go` starts another "thread" to print the message, while the main thread has already ended. Adding `fmt.Scanln()` at the end of the program to prevent the main thread from ending will show all the print content.

### Channels

Channels are the means of communication between goroutines.

```go
func main() {
  message := make(chan string)

  go func() {
    message <- "ping"
  }()

  msg := <-message
  println(msg)
}
```

The `make` function returns a `chan string` type channel. In the anonymous function, the string "ping" is sent into the channel, and then the data in the channel is output to the variable `msg`, finally printing the value of `msg` as "ping".

### Error Handling

In Go, two functions are commonly used for error handling: the `panic` function and the `defer` function. The `panic` function prints an error message and terminates the entire program, similar to Java's Throw Exception; the `defer` function will execute before the current context ends, similar to finally after try-catch; although the `panic` function terminates the entire program, it does not terminate the execution of the `defer` function, which can be used to print logs. Here is a simple example:

```go
func main() {
  println("beginning")
  defer func() {
    println("defer")
  } ()
  println("middle")
  panic("panic")
  println("ending")
}
```

Let's analyze the program's execution result. First, `beginning` is printed; then `defer` is encountered and not printed yet; `middle` is printed before `defer`; encountering `panic`, the program will terminate, printing `defer` and `panic`.

Note that `defer` is executed before the program ends, not
