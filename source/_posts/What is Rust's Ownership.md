---
title: What is Rust's Ownership?
date: 2019-12-21 23:06:28
tags: Programming Languages
---

Rust is memory safe. Facebook's Libra uses Rust for development and has introduced a new programming language called Move. Move's most significant feature is managing digital assets as resources, meaning they can only be moved, not copied, much like paper money, ensuring the safety of digital assets. This idea in Move is not unique; Rust has long used this approach to manage memory, making Rust memory safe. Memory in Rust is managed by the ownership system.

### Java's Reference Counting

Garbage collection comes in many forms, and ownership is one of them. Java uses reference counting, which has a well-known flaw: it cannot reclaim memory space involved in circular references. The basic rule of reference counting is that each time a memory reference is made, the count increases by one, such as instantiating an object or assigning the object to another variable. When a variable reference is canceled, the corresponding count decreases by one until the reference count is zero, at which point the space is freed.

```Java
class Test {
    Test ref = null;
}

Test a = new Test(); // a's count increases by one
Test b = new Test(); // b's count increases by one
// At this point, a's count is 1, b's count is 1

a.ref = b;           // a's count increases by one because ref is a's class variable
b.ref = a;           // b's count increases by one because ref is b's class variable
// At this point, a's count is 2, b's count is 2

a = null;            // a's count decreases by one because a's reference is released
b = null;            // b's count decreases by one because b's reference is released
// At this point, a's count is 1, b's count is 1
```

Therefore, when the references of a and b are released, their counts are still 1. To decrease a.ref's count by one, a.ref needs to point to null. Does this need to be manually set to null? Of course not; Java has no concept of manually releasing memory space. Generally, a.ref's object (b's space) is released (count is 0), and a.ref's count automatically decreases by one, becoming 0. However, because of the circular reference, b needs a's count to become 0 for b's count to become 0. But for a to become 0, b must first become 0. This is essentially a deadlock.

Does this have anything to do with Rust's ownership? Of course not...

### Ownership

Ownership has three basic rules:

- Each value has a variable `owner`.
- Only one `owner` exists at a time.
- When the `owner` leaves the scope, the value's memory space is released.

The scope is generally defined by `{}`, similar to conventional scope concepts.

```Rust
{                       // s is not yet declared
    let s = "hello";    // s is available
}                       // s has left the scope
```

Rust's variable types are divided into simple types and complex types, similar to ordinary variables and reference variables. Due to ownership, when simple types are assigned, the value is copied, but complex types have their references directly reset to new reference variables, rendering the original variable unusable.

```Rust
let x = 5;
let y = x;                        // y is 5, x is still 5

let s1 = String::from("smallyu");
let s2 = s1;                      // s2 is "smallyu", s1 is no longer usable
```

During assignment, s2's pointer first points to the string, then s1's pointer is set to null, embodying the concept of moving. To keep s1 usable, use `clone` to copy data to s2 instead of changing the pointer's direction.

```Rust
let s1 = String::from("smallyu");
let s2 = s1.clone();              // s1 is still usable
```

### Functions

Two concepts mentioned so far are: ownership releases memory space after leaving the scope, and complex type variables are passed in the program by moving. Combining these two features leads to situations like this:

```Rust
fn main() {
    let s = String::from("smallyu");
    takes(s);             // s is passed to the takes function
                          // After takes execution ends, s is released
    println!("{}", s);    // s is unusable, causing a program error
}
fn takes(s: String) {     // s enters the scope
    println!("{}", s);    // s outputs normally
}                         // s leaves the scope, memory space is released
```

If s is assigned a simple type, like 5, this won't happen. For complex type variables, once they leave the scope, their memory space is forcibly released. Currently, we can use function return values to handle this situation:

```Rust
fn main() {
    let s = String::from("smallyu");
    let s2 = takes(s); 
    println!("{}", s2);
}
fn takes(s: String) -> String { 	
    println!("{}", s); 
    s
} 						
```

takes returns the variable unchanged, but a new variable s2 is declared to receive the returned value because s is an immutable variable.

### Reference Variables

Reference variables do not trigger the ownership drop method, meaning they do not reclaim memory space when they leave the scope:

```Rust
fn main() {
    let s = String::from("smallyu");
    takes(&s);

    println!("{}", s);
}

fn takes(s: &String) {
    println!("{}", s);
}
```

### Mutable Variables

Reference variables are read-only, so in takes, s can be accessed but not modified, such as reassigned. Mutable variables can solve this problem:

```Rust
fn main() {
    let mut s = String::from("smallyu");
    takes(&mut s);

    println!("{}", s);
}

fn takes(s: &mut String) {
    s.push_str(", aha!");
}
```

Mutable variables have limitations: only one other variable can reference a mutable variable at a time:

```Rust
let mut s = String::from("smallyu");
let r1 = &mut s;
let r2 = &mut s;
println!("{}, {}", r1, r2);
```

This will cause an error because, to ensure memory safety, a variable can only have one mutable reference. If r1 and r2 could both modify s, it would cause chaos. Therefore, if `r1 = &s` instead of `r1 = &mut s`, there would be no problem, as only one mutable reference is allowed.

### Return Values

Function return types cannot be reference types, also related to ownership rules. Returning ordinary variables is like throwing the function's contents out. If returning a reference variable, it points to something within the function, but once the function ends, everything inside is destroyed, making the reference variable invalid.

```Rust
fn dangle() -> &String {
    let s = String::from("smallyu");
    &s;
} // At this point, s's memory space is released, so the return value cannot reference it
```

### ?

There's no more content.

Recently, I watched an exciting TV show "Silicon Valley." The writers put the main characters in many difficult situations, making them seem like their misfortunes were self-inflicted. The writers also left many cliffhangers, making the plot so intense that I felt like sending them hate mail. Putting aside the plot, the geeks portrayed in the show are really cool! Of course, ordinary people can't participate in such high-level battles.
