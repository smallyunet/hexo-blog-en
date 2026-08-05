---
title: What is a Monad in Haskell?
date: 2019-11-26 13:34:02
tags: Programming Languages
---

The first time I heard about Monad was at a Scala Meetup. Later, I tried to understand the concept of Monad but was overwhelmed by various voluminous books and tutorials on Haskell. Then I came across Ruan Yifeng's article "[Illustrated Monad](http://www.ruanyifeng.com/blog/2015/07/monad.html)" published in 2015. Although it was clear and easy to understand, it was detached from Haskell, and the illustrations didn't match the concepts in the language. Ruan Yifeng's article was translated from "[Functors, Applicatives, And Monads In Pictures](http://adit.io/posts/2013-04-17-functors,_applicatives,_and_monads_in_pictures.html)," which I read through.

### Introduction

Computer programs are used to control a computer to perform calculations. The objects operated on by programs are various types of values, such as numerical values. Here's a simple value `2`:

<img src="1.png" style="box-shadow: 0 0 0 #fff; margin-left: 0;" />

Using a function to process the value can return the result of the function execution, such as:

<img src="2.png" style="box-shadow: 0 0 0 #fff; margin-left: 0;" />

Apart from simple numerical types, values can also be contained within some contextual environment, forming more complex value types. You can think of the contextual environment as a box, with the numerical value placed inside the box. This box as a whole is described as `Just 2`, which is a boxed `2`:

<img src="3.png" style="box-shadow: 0 0 0 #fff; margin-left: 0;" />

If you are familiar with Java, you can think of this box as a wrapper class, like Integer and int, corresponding to boxed and unboxed 2.

### Functors

Facing a boxed `2`, we can't directly apply the `+3` function to it:

<img src="4.png" style="box-shadow: 0 0 0 #fff; margin-left: 0;" />

At this point, we need a function `fmap` to operate on it. `fmap` will first extract the value 2 from `Just 2`, then add 3 to it, put the result 5 back into the box, and return `Just 5`:

<img src="5.png" style="box-shadow: 0 0 0 #fff; margin-left: 0;" />

How does `fmap` know how to parse `Just`? What if it was another type like `Only`, could it still parse it? That's why Functor is needed to complete the definition of this operation.

A Functor is a type of data type:

<img src="6.png" style="box-shadow: 0 0 0 #fff; margin-left: 0;" />

Functor defines the behavior of `fmap`:

<img src="7.png" style="box-shadow: 0 0 0 #fff; margin-left: 0;" />

`fmap` has two input parameters and one output parameter. The input parameters are a function and a boxed value, and the output parameter is a boxed value. It can be used like this:

```Haskell
fmap (+3) (Just 2)
-- Just 5
```

Returning to Haskell, the Haskell "system library" has an instance of `Functor` called `Maybe`, which defines the behavior of `fmap`, specifying how to operate on values when the input parameter is of type `Just`:

```Haskell
instance Functor Maybe where
  fmap func (Just val) = Just (func val)
  fmap func Nothing = Nothing
```

The entire process of the expression `fmap (+3) (Just 2)` is similar to this:

<img src="8.png" style="box-shadow: 0 0 0 #fff; margin-left: 0;" />

Similarly, from the definition of `Maybe`, it can be seen that if the second parameter passed to `fmap` is `Nothing`, the function will return `Nothing`. This is indeed the case:

<img src="9.png" style="box-shadow: 0 0 0 #fff; margin-left: 0;" />

```Haskell
fmap (+3) Nothing
-- Nothing
```

Now suppose a scenario in Java where a user uses a utility class Request to make a request to a server, and the request returns a type Response. Response is an entity class that may or may not contain the required data:

```Java
Response res = Request.get(url);
if (res.get("data") != null) {
  return res.data;
} else {
  return null;
}
```

In Haskell, using `fmap` would become:

```Haskell
fmap (get("data")) (Response res)
```

Of course, Haskell doesn't have a `get("data")` syntax. You can encapsulate the operation of getting `Response.data` as a function `getData`, then pass it into `fmap` as the first parameter.

Haskell provides a syntax sugar `<$>` for `fmap` to simplify its usage:

```Haskell
getData <$> (Response res)
```

Next, think about how Haskell functions operate on lists. The function performs calculations on each element of the list and then returns a list:

<img src="10.png" style="box-shadow: 0 0 0 #fff; margin-left: 0;" />

In fact, lists are also Functors. Here is the definition of lists:

```Haskell
instance Functor [] where
  fmap = map
```

### Applicatives

`Applicatives` is another concept. We previously said that values are placed in boxes. What if functions are also placed in boxes?

<img src="11.png" style="box-shadow: 0 0 0 #fff; margin-left: 0;" />

Haskell provides the operator `<*>` to handle functions inside boxes:

<img src="12.png" style="box-shadow: 0 0 0 #fff; margin-left: 0;" />

For example:

```Haskell
Just (+3) <*> Just 2 == Just 5
```

Using `<*>` can also accomplish some interesting operations, such as multiplying and adding each element in a list:

```Haskell
[(*2), (+3)] <*> [1, 2, 3]
-- [2, 4, 6, 4, 5, 6]
```

<img src="13.png" style="box-shadow: 0 0 0 #fff; margin-left: 0;" />

### Monads

Function execution involves processing values using functions with arguments, involving three roles. `Functors` place the processed value in a box, `Applicatives` place the function in a box, and `Monads` place the function's argument in a box. Monads have an operator `>>=` to achieve Monad functionality. Suppose there is a function `half` that takes a numerical value as an argument. If the value is even, it divides by 2; otherwise, it returns Nothing:

```Haskell
half x = if even x
  then Just (x `div` 2)
  else Nothing
```

<img src="14.png" style="box-shadow: 0 0 0 #fff; margin-left: 0;" />

How do you pass a `Just` type value to `half`?

<img src="15.png" style="box-shadow: 0 0 0 #fff; margin-left: 0;" />

`>>=` can solve this problem:

```Haskell
Just 3 >>= half
-- Nothing
```

The `>>=` operator takes `Just 3`, transforms it into `3`, and processes it in `half`. A `Monad` is a data type that defines the behavior of `>>=`:

```Haskell
class Monad m where
  (>>=) :: m a -> (a -> m b) -> m b
```

<img src="16.png" style="box-shadow: 0 0 0 #fff; margin-left: 0;" />

Here, `Maybe` is a `Monad` (existing alongside the `Maybe` mentioned above):

```Haskell
instance Monad Maybe where
  Nothing >>= func = Nothing
  Just val >>= func = func val
```

`>>=` also supports chain operations:

```Haskell
Just 20 >>= half >>= half >>= half
-- Nothing
```

<img src="17.png" style="box-shadow: 0 0 0 #fff; margin-left: 0;" />

### Summary

Although Haskell's Monad is quite famous, it actually involves three concepts: `Functors`, `Applicatives`, and `Monads`. Perhaps Monad has more extensive applications. In data processing, FP is not superior to OOP; the logic is similar, only the writing style differs. Facing the same problem with different thinking and expressions corresponds to different programming ideas and paradigms. There are many intricate theories in the world waiting for us to explore.
