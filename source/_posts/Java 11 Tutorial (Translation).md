---
title: Java 11 Tutorial (Translation)
date: 2018-10-31 10:20:56
tags: Programming Language
---

Java 11 has been released, and many people are still using Java 8. This tutorial covers some important language features and APIs.

### Local Variable Type Inference

Local variables are those declared within the method body. Java 10 introduced a new keyword `var` to replace type declarations when declaring local variables.

Before Java 10, you had to declare a variable like this:

```java
String text = "Hello Java 9";
```

Now you can use `var` instead of `String`. The compiler will automatically infer the correct type from the variable's assignment. For instance, the type of `text` is inferred as `String`:

```java
var text = "Hello Java 10";
```

Variables declared with `var` are still static and cannot be assigned a different type after declaration:

```java
var text = "Hello Java11";
text = 23;  // Compilation error: incompatible types
```

You can also use `final` to declare constants:

```java
final var text = "Banana";
text = "Joe";   // Compilation error
```

However, `var` cannot be used when the compiler cannot infer the type, such as in these cases:

```java
var a;
var nothing = null;
var lambda = () -> System.out.println("Pity!");
var method = this::someMethod;
```

When variable declarations involve generics, `var` proves particularly advantageous. The following example uses `var` to replace the verbose `Map<String, List<Integer>>`:

```java
var myList = new ArrayList<Map<String, List<Integer>>>();

for (var current : myList) {
    // The type of 'current' is inferred as Map<String, List<Integer>>
    System.out.println(current);
}
```

The `var` keyword in Java 11 also supports use in lambda expression parameters, and you can add annotations to these parameters:

```java
Predicate<String> predicate = (@Nullable var a) -> true;
```

> Tip: In Intellij IDEA, hold down the CTRL key to see the inferred type of a variable.

## HTTP Client

Java 9 introduced a new API, HttpClient, for handling HTTP requests experimentally. Now, Java 11 standardizes it, and it can be accessed from the `java.net` module.

The new HttpClient can be used in both synchronous and asynchronous scenarios. Synchronous requests block the thread until a response is received. BodyHandlers define the type of response data (e.g., `String`, `Byte[]`, `File`).

```java
var request = HttpRequest.newBuilder()
        .uri(URI.create("https://blog.smallyu.net"))
        .GET()
        .build();
var client = HttpClient.newHttpClient();
var response = client.send(request, HttpResponse.BodyHandlers.ofString());
System.out.println(response.body());

// Remember to import the java.net.http module in module-info.java
```

You can also make asynchronous requests. Calling `sendAsync` will not block the current thread; it constructs an asynchronous operation chain and executes the corresponding action upon receiving a response:

```java
var request = HttpRequest.newBuilder()
        .uri(URI.create("https://blog.smallyu.net"))
        .build();
var client = HttpClient.newHttpClient();
client.sendAsync(request, HttpResponse.BodyHandlers.ofString())
        .thenApply(HttpResponse::body)
        .thenAccept(System.out::println);

// Sleep the thread to prevent it from ending before receiving the response
Thread.sleep(3000);
```

> The `.GET()` method is the default request method.

The next example demonstrates how to send a request using the POST method to a specified URL. Similar to `BodyHandlers`, `BodyPublishers` defines the type of data to be sent:

```java
var request = HttpRequest.newBuilder()
        .uri(URI.create("https://postman-echo.com/post"))
        .header("Content-Type", "text/plain")
        .POST(HttpRequest.BodyPublishers.ofString("Hi there!"))
        .build();
var client = HttpClient.newHttpClient();
var response = client.send(request, HttpResponse.BodyHandlers.ofString());
System.out.println(response.statusCode());      // 200
```

The last example shows how to perform authentication using BASIC-AUTH:

```java
var request = HttpRequest.newBuilder()
    .uri(URI.create("https://postman-echo.com/basic-auth"))
    .build();
var client = HttpClient.newBuilder()
    .authenticator(new Authenticator() {
        @Override
        protected PasswordAuthentication getPasswordAuthentication() {
            return new PasswordAuthentication("postman", "password".toCharArray());
        }
    })
    .build();
var response = client.send(request, HttpResponse.BodyHandlers.ofString());
System.out.println(response.statusCode());      // 200
```

### Collections Framework

Collections frameworks like List, Set, and Map have new methods added. `List.of` creates an immutable list from the given parameters, and `List.copyOf` creates a copy of an existing list.

```java
var list = List.of("A", "B", "C");
var copy = List.copyOf(list);
System.out.println(list == copy);   // true
```

Since the list is immutable, the copied list and the original list are the same instance. If a mutable list is copied, the copied list will be a new instance without affecting the original list:

```java
var list = new ArrayList<String>();
var copy = List.copyOf(list);
System.out.println(list == copy);   // false
```

To create an immutable map, simply alternate the keys and values as parameters:

```java
var map = Map.of("A", 1, "B", 2);
System.out.println(map);    // {B=2, A=1}
```

> The immutable lists in Java 11 use the same interface as lists in older versions, but modifying an immutable list (e.g., adding or removing elements) will throw a `java.lang.UnsupportedOperationException`. Fortunately, when you try to modify an immutable list, Intellij IDEA will check and warn you.

### Streams

Streams, introduced in Java 8, now have three new methods. `Stream.ofNullable` creates a stream from a single element:

```java
Stream.ofNullable(null)
    .count()   // 0
```

`dropWhile` and `takeWhile` methods are used to discard some elements in the stream:

```java
Stream.of(1, 2, 3, 2, 1)
    .dropWhile(n -> n < 3)
    .collect(Collectors.toList());  // [3, 2, 1]

Stream.of(1, 2, 3, 2, 1)
    .takeWhile(n -> n < 3)
    .collect(Collectors.toList());  // [1, 2]
```

### Strings

The `String` class also has several new methods:

```java
" ".isBlank();                // true
" Foo Bar ".strip();          // "Foo Bar"
" Foo Bar ".stripTrailing();  // " Foo Bar"
" Foo Bar ".stripLeading();   // "Foo Bar "
"Java".repeat(3);             // "JavaJavaJava"
"A\nB\nC".lines().count();    // 3
```

### Other JVM Features

Java 11 includes many new features; the above are just the tip of the iceberg. There is much more to explore...

### References

- [Java 11 Tutorial](https://winterbe.com/posts/2018/09/24/java-11-tutorial/)
