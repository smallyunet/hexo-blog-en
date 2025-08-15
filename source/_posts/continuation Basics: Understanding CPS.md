---
title: "continuation Basics: Understanding CPS"
date: 2025-07-23 12:12:12
draft_date: 2025-07-19 01:10:18
tags: 
- continuation
- Tutorial
---

Let's systematically explore the principles and application scenarios of continuations from basic to advanced. This tutorial series is entirely my own study and research results, and has nothing to do with YIN Wang's continuation course, so there are no copyright issues. Of course, it is precisely because I studied the foundational course and built a solid base that I could learn and understand the concept of continuation on my own. This article will reveal a few foundational skills learned from the course, since continuation is an advanced topic from the basic class and cannot be understood without them.

### Background

I realized something — I've written tons of asynchronous code in Node.js, whether it's reading files or calling APIs, and it's common to see code like this:

```js
fs.readFile("a.txt", (err, data) => {
  // do something
});
```

But no one ever told me this is called CPS!

I recently started looking into CPS because I was curious about the continuation and concurrent computing course by YIN Wang. I didn’t enroll in the course, but I wanted to know what it covered and what continuation actually meant.

So what is a continuation? CPS stands for Continuation-Passing Style, and as it turns out, we’ve been using CPS all along in Node.js to handle asynchronous calls — it's not so mysterious anymore!

### Recursion

First, here’s a recursive version of the factorial function `fact`, which we’re all familiar with:

```js
function fact(n)
{
  if (n 0) 
  {
    return 1;
  }
  else
  {
    return n * fact(n - 1);
  }
}

console.log("fact1=", fact(1)); // 1
console.log("fact3=", fact(3)); // 6
console.log("fact5=", fact(5)); // 120
```

### Tail Recursion

Next, let's rewrite the `fact` function in a tail-recursive style. Tail recursion adds a new parameter to carry the intermediate result:

```js
function factTail(n, prod)
{
  if (n == 0)
  {
    return prod;
  }
  else
  {
    return factTail(n-1, prod*n);
  }
}

console.log("factTail1=", factTail(1, 1)); // 1
console.log("factTail3=", factTail(3, 1)); // 6
console.log("factTail5=", factTail(5, 1)); // 120
```

### CPS Style

Building upon the tail-recursive version of `fact`, we add a new parameter `k`, a function that gets called with the result instead of returning it directly:

```js
function factTailCPS(n, prod, k)
{
  if (n == 0)
  {
    return k(prod);
  }
  else
  {
    return factTailCPS(n-1, prod*n, k);
  }
}

factTailCPS( 1, 1, x => console.log("factTailCPS1=", x) ); // 1
factTailCPS( 3, 1, x => console.log("factTailCPS1=", x) ); // 6
factTailCPS( 5, 1, x => console.log("factTailCPS1=", x) ); // 120
```

This `k` is the continuation — it tells the `fact` function what to do next with the result. This is a full example of CPS (Continuation-Passing Style).

### Typical CPS

However, this tail-recursive + continuation style is still not the most canonical CPS form. Let’s look at a more "typical" CPS implementation of the recursive `fact` function:

```js
function factCPS(n, k)
{
  if (n == 0)
  {
    return k(1);
  }
  else
  {
    return factCPS(n-1, r => k(n * r));
  }
}

This might be a bit confusing at first, so let’s break it down. The `k` is still the continuation function. Here's how to call it:

```js
let factCPS1 = factCPS(0, x => x);
console.log("factCPS1=", factCPS1); // 1

let factCPS3 = factCPS(3, x => x);
console.log("factCPS3=", factCPS3); // 6

let factCPS5 = factCPS(5, x => x);
console.log("factCPS5=", factCPS5); // 120
```

The key is passing `x => x` as the second argument — a simple identity function. Let’s walk through calculating the factorial of 2 step by step:

```js
let factCPS2 = factCPS(2, x => x);
console.log("factCPS2=", factCPS2); // 2

// n=2, k=x=>x, return factCPS(1, r => k(2 * r));
  // n=1, k=r=>(x=>x)(2*r), return factCPS(0, r => k(1 * r));
    // n=0, k=r=>(r=>(x=>x)(2*r)(1*r)), return k(1);
      // k(1) = r=>(x=>x)(2*r)(1*1)
      //      = (x=>x)(2)
      //      = 2
```

Even though this is the correct step-by-step breakdown, it’s still hard to grasp — give it a try yourself to truly understand the typical CPS call flow. This could take a few hours — that’s totally normal.

To summarize, every CPS call stores the current step’s value in a closure. Tail recursion passes values via parameters, while CPS uses closures to pass values to the next step — provided you understand what closures are, of course, which is another key concept in the basic class, especially when implementing interpreters.

### CPS for the `fib` Function

The factorial function `fact` only has one recursive call per execution. Now let’s look at the Fibonacci sequence, where each call has **two** recursive calls. Here’s the recursive version:

```js
function fib(n)
{
  if (n == 0)
  {
    return 0;
  }
  else if (n == 1)
  {
    return 1;
  }
  else 
  {
    return fib(n-1) + fib(n-2);
  }
}

console.log("fib(2)=", fib(2)); // 1
console.log("fib(5)=", fib(5)); // 5
```

Now here's the CPS version of `fib`, and how it works:

```js
function fibCPS(n, k)
{
  if (n == 0)
  {
    return k(0);
  }
  else if (n == 1)
  {
    return k(1);
  }
  else
  {
    return fibCPS(n-1, r1 => fibCPS(n-2, r2=>k(r1+r2)) );
  }
}

As you can see, the second recursive call is embedded inside the first one’s continuation. It might sound a bit confusing, but follow the code closely and it will make more sense.

Here’s how to call the CPS version of `fib`:

```js
let fibCPS1 = fibCPS(1, x=>x);
console.log("fibCPS1=", fibCPS1); // 1

let fibCPS2 = fibCPS(2, x=>x);
console.log("fibCPS2=", fibCPS2); // 1

let fibCPS4 = fibCPS(4, x=>x);
console.log("fibCPS4=", fibCPS4); // 3

let fibCPS5 = fibCPS(5, x=>x);
console.log("fibCPS5=", fibCPS5); // 5
```

Now let’s walk through calculating `fib(3)` using CPS. This is more complex than the `fact` example:

```js
let fibCPS3 = fibCPS(3, x=>x);
console.log("fibCPS3=", fibCPS3); // 1+1=2

// n=3, k=x=>x, 
       // return fibCPS(2, r1 => fibCPS(1, r2=>(x=>x)(r1+r2)) );
// n=2, k= r1 => fibCPS(1, r2=>(x=>x)(r1+r2)), 
       // return fibCPS(1, r1 => fibCPS(0, r2 => ( r1 => fibCPS(1, r2=>(x=>x)(r1+r2)) )(r1+r2)) );
// n=1, k= r1 => fibCPS(0, r2 => ( r1 => fibCPS(1, r2=>(x=>x)(r1+r2)) )(r1+r2)), 
       // return k(1)
       // return ( r1 => fibCPS(0, r2 => ( r1 => fibCPS(1, r2=>(x=>x)(r1+r2)) )(r1+r2)) )(1)
       // return fibCPS(0, r2 => ( r1 => fibCPS(1, r2=>(x=>x)(r1+r2)) )(1+r2))
          // n=0, k= r2 => ( r1 => fibCPS(1, r2=>(x=>x)(r1+r2)) )(1+r2)
              // return k(0)
              // return ( r2 => ( r1 => fibCPS(1, r2=>(x=>x)(r1+r2)) )(1+r2) )(0)
              // return ( r1 => fibCPS(1, r2=>(x=>x)(r1+r2)) ) (1+0)
              // return fibCPS(1, r2=>(x=>x)(1+r2))
                  // n=1, k = r2=>(x=>x)(1+r2)
                  // return k(1)
                  // return (x=>x)(1+1)
                  // return 2
```

With the examples of `fact` and `fib`, we now understand the CPS form and its detailed execution steps. Understanding CPS is just the beginning — we’ll use continuations to build even more interesting programs later.

### Exercise

Here’s a recursive function `sumFrom` that computes `a+(a+1)+...+(b-1)+b`:

```js
function sumFrom(a, b)
{
  if (a == b) 
  {
    return a;
  }
  else
  {
    return b + sumFrom(a, b-1);
  }
}

console.log(sumFrom(1, 3));   // 6
console.log(sumFrom(2, 5));   // 14
```

Your task: Convert `sumFrom` into CPS form by completing the `sumFromCPS` function:

```js
function sumFromCPS(a, b, k)
{
  // ____
}

sumFromCPS(1, 3, x => console.log(x));   // 6
sumFromCPS(2, 5, x => console.log(x));   // 14
```

### Further Reading

We’ve manually transformed recursive code into CPS. But there are ways to **automatically** transform code into CPS — the legendary “YIN Wang’s 40 lines of code” does just that. Check out these links:

- [Is YIN Wang’s "40 lines of code" really that powerful?](https://www.zhihu.com/question/20822815)
- [GTF - Great Teacher Friedman](https://www.yinwang.org/blog-cn/2012/07/04/dan-friedman)

Since automatic CPS transformation is quite complex, I’m not planning to learn or implement it myself.
