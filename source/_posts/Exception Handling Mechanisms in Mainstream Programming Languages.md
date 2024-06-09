---
title: Exception Handling Mechanisms in Mainstream Programming Languages
date: 2019-04-24 10:19:04
tags: Programming Languages
---

When learning a programming language, it is important to start with its features rather than the language itself. Here, we attempt a simple, horizontal understanding of the exception and error handling mechanisms of various programming languages. The languages involved include C, C++, Go, Java, Scala, Kotlin, Ruby, Rust, JavaScript, PHP, Python, and Lisp.

### C

The C language does not have an exception handling mechanism. When an error occurs, a global error code `errno` is set. C provides the `perror()` and `strerror()` functions to display the description related to `errno`. The `perror()` function can be called directly, with a string parameter, and outputs `parameter: error text`. The `strerror()` function takes a number (error code) as a parameter and returns a pointer to the corresponding error text.

```C
#include <stdio.h>
#include <errno.h>
#include <string.h>

void main ()
{
  // Opening a non-existent file will cause an error
  fopen ("unexist.txt", "rb");

  // 2
  printf("%d\n", errno);

  // No such file or directory
  perror("");

  // No such file or directory
  printf("%s\n", strerror(errno));
}
```

### C++

C++ supports exception handling mechanisms. C++ can throw or catch two types of content: int or char* types, which can be caught and thrown by the program. This differs from Java, which does not support directly throwing basic types:

```C++
#include <iostream>
#include <exception>
using namespace std;
int main () {
    try
    {
        throw "error";
    }
    catch(const char* msg)
    {
        cout << msg << endl;
    }
} 

// error
```

Another type is classes, which can be built-in standard exception classes or custom exception classes:

```C++
#include <iostream>
#include <exception>
using namespace std;
int main () {
    try
    {
        throw exception();
    }
    catch(std::exception& e)
    {
        cout << e.what() << endl;
    }
} 

// std::exception
```

### Go

Go, as a non-OOP language, does not support try-catch syntax but still has similar throw and catch features. Go has three error-related keywords: `panic()`, `recover()`, and `defer`. `panic()` throws an exception, `recover()` catches the exception, and the `defer` keyword defines what will be executed last, similar to `finally`:

```Go
package main
import "fmt"

func main() {
  defer func() {
    err := recover()
    fmt.Println(err)
  }()
  panic("error")
}

// error
```

### Java

Java, being a pure OOP language, only supports throwing and catching objects:

```Java
public class ErrorTest {
    public static void main(String[] args) {
        try {
            throw new Exception();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

// java.lang.Exception
```

### Scala

Scala and Java belong to the same category, supporting only throwing and catching objects. The syntax is slightly different from Java, but the concept is fundamentally the same:

```Scala
object ErrorTest {
  def main(args: Array[String]): Unit = {
    try {
      throw new Exception()
    } catch {
      case e: Exception => print(e)
    }
  }
}

// java.lang.Exception
```

Moreover, Scala throws Java exceptions. Scala might not be considered an independent programming language but rather one that provides syntax sugar for Java. This point is worth deeper thinking and exploration.

### Kotlin

Kotlin is similar to Scala in nature, also throwing Java exceptions by default:

```Kotlin
fun main(args: Array<String>) {
  try {
    throw Exception()
  } catch (e: Exception) {
    print(e)
  }
}

// java.lang.Exception
```

### Ruby

Ruby uses the `raise` and `rescue` keywords instead of `try` and `catch` to implement throwing and catching exceptions. Ruby also supports the try-catch keywords, but they are not discussed here as I haven't figured out their usage yet.

```Ruby
begin
  raise "error" 
  rescue Exception => e
    puts e
end

// error
```

### Rust

Rust does not have try-catch syntax or Go-like error handling functions. Instead, it uses `Option<T>` or the enhanced `Result<T, E>` for error handling. Rust's pattern matching is similar to Scala's:

```Rust
fn main() {
  match find() {
    None => println!("none"),
    Some(i) => println!("{}", i),
  }
}

fn find() -> Option<usize> {
  if 1 == 1 {
    return Some(1);
  }
  None
}

// 1
```

### JavaScript

Scripting languages do not enforce type constraints on variables, so exceptions cannot be distinguished by type. The content of the thrown error is relatively free:

```JavaScript
try {
  throw 1
} catch (e) {
  console.log(e)
}

// 1

try {
  throw new Error('')
} catch (e) {
  console.log(e)
}

// Error
```

### PHP

PHP's try-catch is similar to Java, with no special features:

```PHP
<?php
  try {
      throw new Exception("error");
  } catch (Exception $e) {
      echo $e->getMessage();
  }
```

### Python

Python's syntax shows the influence of Ruby. `raise` triggers an exception, and `except` catches it:

```Python
try:
  raise
except:
  print("error")
```

### Lisp

Lisp is generally more complex. The content of Lisp's exception handling is temporarily left as an open question. The following is one of the situations in Common Lisp that triggers an error. `declare` declares the type of function parameters, and passing the wrong parameters will cause an error:

```Lisp
(defun df (a b)
  (declare (double-float a b))
  (* a b))
  
(df "1" 3)

// *** - *: "1" is not a number
```

### Future Discussion

Initially, I wanted to sort out most of the concepts related to exceptions and error handling in these languages. However, I found it quite difficult and also failed to distinguish between "exception" and "checked exception," leading to potential deviations in intention, title, and content. This time, I'll just mention "exception," and discuss "checked exception" in future content.
