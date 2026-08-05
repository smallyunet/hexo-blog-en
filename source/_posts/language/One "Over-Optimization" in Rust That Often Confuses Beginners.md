---
title: One "Over-Optimization" in Rust That Often Confuses Beginners
tags:
  - Rust
  - Programming Language
date: 2025-06-30 02:05:25
draft_date: 2025-06-30 01:02:10
---

Suppose we want to write some code. Just use `cargo new` to create a project, and then define a function called `append`. The function is straightforward—it concatenates two input strings. The first parameter is a reference to a string, and the second is also a string. For example, given the parameters `Hello` and `, world`, the function will return `Hello, world`. Here's the function:

```rust
fn append(s1: &String, s2: &String) -> String {
    return s1.clone() + s2.clone().as_str();
}
```

Don’t worry about the syntax after `return`; that’s not our focus here. In the `main` function, we call `append`, run the code, and the output will be as expected—`Hello, world`:

```rust
fn main() {
    let s1: String = String::from("Hello");
    let s2: String = String::from(", world");
    println!("{}", append(&s1, &s2));
}
```

Now, keep the `append` function exactly the same, but change the string definitions in `main`. The `main` function becomes:

```rust
fn main() {
    let s1: Box<String> = Box::new(String::from("Hello"));
    let s2: Box<String> = Box::new(String::from(", world"));
    println!("{}", append(&s1, &s2));
}
```

Our initial instinct might be that this should cause a compile-time error, since `s1` is of type `Box<String>`, and passing `&s1` means the type is `&Box<String>`, which doesn't match the `append` function's parameter type of `&String`. So why does this compile successfully and output `Hello, world` as expected? (Ignore what `Box` is for now; it's just another type.)

Let's take it further and modify `main` like this:

```rust
fn main() {
    use std::rc::Rc;

    let s1: Rc<String> = Rc::new(String::from("Hello"));
    let s2: Rc<String> = Rc::new(String::from(", world"));
    println!("{}", append(&s1, &s2));
}
```

Will this compile? Will it run correctly? The `append` function definition hasn't changed. Here, `s1` is of type `Rc<String>`, and the arguments passed into `append` are of type `&Rc<String>`. So why doesn't the compiler complain, and why does it still print `Hello, world`? (Again, ignore what `Rc` is—just treat it as a type.)

From the above code snippets, we observe a phenomenon: when the function parameters are of type `&String`, it accepts not only `&String` but also `&Box<String>` and `&Rc<String>`.

Let’s push this further. What happens if we change `main` to:

```rust
fn main() {
    let s1: Box<Box<Box<Box<String>>>> = Box::new(Box::new(Box::new(Box::new(String::from("Hello")))));
    let s2: Box<Box<Box<Box<String>>>> = Box::new(Box::new(Box::new(Box::new(String::from(", world")))));
    println!("{}", append(&s1, &s2));
}
```

Or like this:

```rust
fn main() {
    use std::rc::Rc;
    
    let s1: Rc<Rc<Rc<Rc<String>>>> = Rc::new(Rc::new(Rc::new(Rc::new(String::from("hello")))));
    let s2: Rc<Rc<Rc<Rc<String>>>> = Rc::new(Rc::new(Rc::new(Rc::new(String::from(", world")))));
    println!("{}", append(&s1, &s2));
}
```

The result: both versions of `main` compile and run normally, printing `Hello, world`.

To explore the type behavior further, let’s define two more `append` functions. `append2` takes `&Box<String>`, and `append3` takes `&Rc<String>`:

```rust
fn append2(s1: &Box<String>, s2: &Box<String>) -> Box<String> {
    let mut result = (**s1).clone();
    result.push_str(s2);
    Box::new(result)
}

use std::rc::Rc;
fn append3(s1: &Rc<String>, s2: &Rc<String>) -> Rc<String> {
    let mut result = (**s1).clone();
    result.push_str(s2);
    Rc::new(result)
}
```

Now, consider the following `main` function. On which line will the compiler report an error?

```rust
fn main() {
    let s1: Box<Box<Rc<Rc<String>>>> = Box::new(Box::new(Rc::new(Rc::new(String::from("hello")))));
    let s2: Box<Box<Rc<Rc<String>>>> = Box::new(Box::new(Rc::new(Rc::new(String::from(", world")))));
    println!("{}", append(&s1, &s2));
    println!("{}", append2(&s1, &s2));
    println!("{}", append3(&s1, &s2));
}
```

What if we expand the types even further? Will the compiler still complain, and where?

```rust
fn main() {
    let s1: Box<Box<Rc<Rc<Box<Box<String>>>>>> = Box::new(Box::new(Rc::new(Rc::new(Box::new(Box::new(String::from("hello")))))));
    let s2: Box<Box<Rc<Rc<Box<Box<String>>>>>> = Box::new(Box::new(Rc::new(Rc::new(Box::new(Box::new(String::from(", world")))))));
    println!("{}", append(&s1, &s2));
    println!("{}", append2(&s1, &s2));
    println!("{}", append3(&s1, &s2));
}
```

Rust calls this ergonomic design, meant to reduce the developer’s burden. However, when it comes to things like frequent ownership moves or needing to annotate lifetimes with `'`, Rust drops ergonomic considerations in favor of memory safety. Arguably, that's not wrong—after all, memory safety is Rust’s non-negotiable priority.

Finally, let’s raise the difficulty. In a real-world scenario, suppose there's a function called `do_something` that takes generic parameters. The original logic looks like this:

```rust
fn do_something<T1, T2>(t1: T1, t2: T2) {
    println!("{}", append(&t1, &t2));
}
```

Now let’s add some extra processing:

```rust
fn do_something<T1, T2>(t1: T1, t2: T2) {
    // Add a function to process t1
    handle_t1(&t1);  

    println!("{}", append(&t1, &t2));
}
```

So here’s the question: What is the type of parameter `t1`? How should the `handle_t1` function be defined? In the original logic, `t1` is passed to `append`, so does that mean `t1` is of type `&String`? If not, what could `t1`’s type be?
