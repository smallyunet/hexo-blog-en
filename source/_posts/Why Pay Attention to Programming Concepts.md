---
title: Why Pay Attention to Programming Concepts
tags: Programming Languages
date: 2022-07-24 22:53:55
draft_date: 2022-07-24 21:21:40
---

### 1

A couple of days ago, I encountered a small problem: a Solidity smart contract exceeding 24 KB couldn't be deployed on the Ethereum mainnet due to EVM's code size limitation. So, I considered how to reduce the contract size, but I was quite unclear about the concept of contract size.

I noticed that a contract can include other contracts and call their methods, just by passing the deployed contract's address as a parameter to the contract:

```solidity
contract Demo {}
contract Main {
    Demo demo;
    constructor(Demo _demo) public {
        demo = _demo;
    }
}
```

Does the contract size include the imported contracts? When EVM executes the contract, does it load the code of other contracts first and then run them together? Does the code size calculation include all contracts? That would be troublesome.

Later, I realized that interfaces can be used instead of contracts:

```solidity
contract IDemo {}
contract Demo is IDemo {}
contract Main {
    IDemo demo;
    contract(IDemo _demo) public {
        demo = _demo;
    }
}
```

The code for an interface is certainly less than the concrete implementation because the interface doesn't contain method bodies. Replacing all imported contracts with interfaces would make the contract much smaller, wouldn't it?

Of course, the concern here isn't how to write Solidity contracts or how to calculate contract code size, which I later figured out. What puzzled me was the sudden realization of the difference between using interfaces and using contracts.

Previously, I defined interfaces for contracts to provide a description of external methods. Here, I realized that interfaces could replace contracts themselves, directly defining variables and using those interface-defined variables to call methods within the contract. But why is that possible? Isn't it just an interface?

### 2

If you've recently learned Java or use Java as your working language, you might find this problem laughably simple. Isn't this just polymorphism?

In the first Java class, the teacher tells us that object-oriented programming has three main features: encapsulation, inheritance, and polymorphism. I can still remember this fundamental concept today. Yet, after years of work, I found myself puzzled by such a simple problem in practical work, failing to grasp the meaning of using an interface as a type. This is absurd. Maybe it's because I haven't written Java in a long time and have been using Golang instead.

Java is undeniably the benchmark for object-oriented programming languages. Solidity, though a seemingly new scripting language for smart contracts, combines features from multiple languages but is fundamentally based on object-oriented principles. Contracts are classes, deploying a contract is instantiating an object, a contract address is the object's memory address, and calling a contract is calling a method on the object...

Any object-oriented programming language includes object-oriented features and can use object-oriented writing styles, relying on basic features like polymorphism. From an object-oriented perspective, what's difficult about Solidity? It's merely a change in form, with programming logic remaining the same, plus some blockchain-specific concepts like transactions and block height.

From a programming language perspective, Solidity can't compare to mature languages like Java. Its object-oriented features are incomplete, and constructs like modifier and require, though seemingly convenient, add a lot to the cognitive load and make the code structure less uniform. How can EVM compare to JVM? Yet, as a lightweight scripting language, Solidity uses redundant static typing.

It's essential to remember that programming concepts precede programming languages. I still believe that formal programming languages [aren't worth learning](http://smallyu.net/2022/02/16/%E6%88%91%E4%B8%BA%E4%BB%80%E4%B9%88%E4%B8%8D%E5%AD%A6%E4%B9%A0%E7%BC%96%E7%A8%8B%E8%AF%AD%E8%A8%80/), but I don't deny that learning programming concepts can start from learning programming languages. For example, the concept of [polymorphism](https://en.wikipedia.org/wiki/Polymorphism_(computer_science)) involves using a unified symbol to represent different types, including three interpretations: supporting multiple types of parameters (method overloading in Java), supporting multiple types of parameters (generics in Java), and subtypes (using interfaces as types, as in the polymorphism scenario mentioned above).

Object-oriented programming is a programming concept encompassing many computer science ideas, and Java is a fully object-oriented language. It not only covers many useful features but also implements them thoroughly and elegantly. If you've learned Java, you'll naturally understand object-oriented principles, which are infinitely beneficial. From this perspective, does Golang have anything worth learning compared to Java? Is it the struct syntax or the use of the * symbol? Perhaps Golang is more of a fast-food language, allowing convenient go func(). However, it's not highly recommended for learning purposes.

Spending a few minutes browsing the Java documentation, I quickly recall the content since it's so fundamental. It's also a reminder to myself not to forget how to code.
