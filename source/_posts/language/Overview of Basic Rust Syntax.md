---
title: Overview of Basic Rust Syntax
date: 2019-08-19 22:12:25
tags: Programming Language
---

Rust is a language with complexity and application scenarios comparable to C++. Let's learn it together!

Recently, I've been pondering what kind of content this type of article represents. A programming language tutorial? The content is not comprehensive enough; an evaluation of the language? Not quite; study notes? If so, that's definitely not my intention. I tend to think this is an exploratory process, whether for myself or for others. I hope to show that, look, there's nothing mysterious about a new programming language, it's so simple! Some programmers spend their entire lives using a particular language as a prefix to their job title, "Java Programmer" or "Backend Developer." We should break out of this loop.

### Statements

Rust statements must end with a `;`.

### Constants and Variables

Rust uses `let` to define constants and `let mut` to define variables. This way of writing may seem a bit strange:

```Rust
fn main() {
  let x = 1;
  println!("{}", x);

  let mut y = 2;
  println!("{}", y);

  y = 3;
  println!("{}", y);
}
```

Unlike other languages, Rust allows multiple declarations of the same constant within the same scope. This means that although constants in Rust cannot be reassigned, the same constant name can be defined multiple times. We can understand the difference between constants and variables at the system level, but the writing style can be a bit confusing. If I assign a value to the same symbol multiple times, isn't that symbol a variable?

```Rust
fn main() {
  let x = 1;
  println!("{}", x);

  let x = 2;
  println!("{}", x);
}
```

Another somewhat strange aspect is that Rust variables cannot be redeclared. We cannot speculate on the intentions of the language designers; this is clearly not allowed to enable redefinition. Perhaps, there are only constants in Rust, and the `mut` keyword provides an interface for constants to be assigned multiple times. Without `mut`, a constant is just a constant; with `mut`, the constant gets an "entry" to acquire new values. As for the issue of variable redeclaration, why need a bicycle?

```Rust
fn main() {
  let mut x = 1;
  let mut x = 2;
}
// warning: variable does not need to be mutable
```

### Control Flow

The conditional part of Rust does not require parentheses, similar to the Go language. Who came first?

```Rust
fn main() {
  let number = 2;
  if number == 1 {
    println!("1")
  } else if number == 2 {
    println!("2")
  } else {
    println!("3")
  }
}
```

Since the `if` statement itself is an expression, it can also be nested into assignment statements to achieve a function similar to the ternary operator in other languages. (Rust is a strongly typed language, so assignment types must be consistent.)

```Rust
fn main() {
  let number = if true {
    3
  } else {
    4
  };
  println!("{}", number);
}
```

Compared to the concise and multifunctional `for` loop of the Go language, Rust supports multiple types of loops:

```Rust
fn main() {
  loop {
    // ...
  }

  while true {
    // ...
  }

  let a = [1, 2, 3];
  for item in a.iter() {
    println!("{}", item);
  }
}
```

### Functions and Value Passing

Rust seems to have no distinction between pass by value and pass by reference, because everything in Rust is passed by reference, or categorized as constant passing and variable passing. Comparing the creation of strings in Java, strings in Rust can also be created using the "object declaration" method:

```Rust
fn main() {
  // Constant passing
  let a = String::from("a");
  testa(&a);

  // Variable passing
  let mut b = String::from("b");
  testb(&mut b);
  println!("{}", b);
}

fn testa(a: &String) {
  println!("{}", a);
}

fn testb(b: &mut String) {
  b.push_str(" b");
}
```

Functions can also have return values, and in Rust, the return type is defined with `->`. By default, the value of the last line of the function is used as the return value, or you can use `return` to end the function early. Note that the expression used as the return value on the last line should not end with a semicolon...

```Rust
fn main() {
  let mut a = test();
  println!("{}", a);

  a = test2();
  println!("{}", a);
}

fn test() -> u32 {
  1
}

fn test2() -> u32 {
  return 2;
}
```

### Structures

The basic usage of structures is quite conventional, without the `new` keyword; you can use it directly by "instantiating" it:

```Rust
struct Foo {
  a: String,
  b: i32
}

fn main() {
  let t = Foo {
    a: String::from("a"),
    b: 1,
  };

  println!("{}, {}", t.a, t.b);
}
```

You can also add methods to structures:

```Rust
struct Foo {
  a: String,
  b: i32
}

impl Foo {
  fn test(&self) -> i32 {
    self.b + 1
  }
}

fn main() {
  let t = Foo {
    a: String::from("a"),
    b: 1,
  };

  println!("{}, {}, {}", t.a, t.b, t.test());
}

// a, 1, 2
```

### Lists and Pattern Matching

The following example creates a vector containing three elements, then assigns the 0th element to the constant `one`. It then uses pattern matching to check if the 0th element of the list equals the value of `one`. If they are equal, it outputs the string "one"; otherwise, it outputs "none." In Rust's pattern matching, `Some()` and `None` are built-in keywords:

```Rust
fn main() {
  let v = vec![1, 2, 3];

  let one = &v[0];
  println!("{}", one);

  match v.get(0) {
    Some(one) => println!("one"),
    Some(2) => println!("two"),
    None => println!("none"),
  }
}
```

### Error Handling

The `panic` function is used to throw exceptions:

```Rust
fn main() {
  panic!("new Exception");
}
// thread 'main' panicked at 'new Exception', test.rs:4:3
// note: Run with `RUST_BACKTRACE=1` environment variable to display a backtrace.
```

For error handling, Rust provides two shorthand methods for conveniently handling error information. The `unwrap()` function automatically throws a `panic`. If `unwrap()` is not used, the program skips the code where `panic` occurred. This is somewhat opposite to Java's exception handling logic because if an exception is not handled in Java, the program cannot continue running. In Rust, if `unwrap()` is used to handle a `panic`, the program will stop executing and print the error message.

```Rust
use std::fs::File;

fn main() {
  let f = File::open("hello.txt");
  println!("a");

  let f2 = File::open("hello.txt").unwrap();
  println!("b");
}

// a
// thread 'main' panicked at 'called `Result::unwrap()` on an `Err` value: Os { code: 2, kind: NotFound, message: "The system cannot find the file specified." }', src\libcore\result.rs:999:5
// ...
```

Another shorthand method is `expect()`, which can be used to replace `unwrap()`. The difference between `expect()` and `unwrap()` is that `unwrap()` uses the system's built-in `panic` information, while `expect()` can pass a parameter as the `panic` error message. That's all.

```Rust
use std::fs::File;

fn main() {
    let f = File::open("hello.txt").expect("Failed to open hello.txt");
}

// thread 'main' panicked at 'Failed to open hello.txt: ...
// ...
```

### Lambda Expressions

Lambda expressions in Rust use `|` as parameter delimiters, replacing `()`. Besides that, Lambdas function the same as in other languages:

```Rust
fn main() {
  let test = |num| {
    num == 1
  };

  println!("{}, {}", test(1), test(2));
}
// true, false
```

### Others

Rust's language features go far beyond this, especially Rust's unique memory management mechanism, and the concept of "ownership," which makes it hard for Rust beginners to get started, require us to keep moving forward.
