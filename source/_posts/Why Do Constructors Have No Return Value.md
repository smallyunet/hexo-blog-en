---
title: Why Do Constructors Have No Return Value?
date: 2020-05-16 12:19:44
tags: design
---

Just now, a classmate asked me a question: What does it mean to `new` a member function in C++?

...I thought to myself, a `new` member function? Is there such an operation?

Later I asked, is the name of this member function the same as the class name?

He said yes, it's the constructor.

...Isn't that just `new`ing an object?

Then he asked a profound question: Why don't constructors have return values?

I said that `new` is for the class, the part after `new` refers to the class name, not a member function.

But the class doesn't have parameters?

The constructor has parameters.

But the constructor has no return value?

Huh?

<img src="face.jpeg" width="30%" />

Here is a simple piece of code:

``` C++
#include <iostream>
using namespace std;

class Test
{
    public:
        Test(int num);
};

// Constructor, has parameters but no return value
Test::Test(int num)
{
    cout << num << endl;
}

int main()
{
    // Object assigned to a variable
    Test* test = new Test(1);
    return 0;
}
```

Actually, you can think of this scenario without looking at the code. When you `new` an object, is the variable after `new` referring to the class name or the method name?

If it refers to the class name, the class itself has no parameters, and no return value. Moreover, the behavior of the `new` keyword in the code would be different from other statements. Additionally, the class has a default no-argument constructor. If `new` is for the class, why do we need the default constructor?

If it refers to the function name, the constructor has no return value. As in the code above, it's clear that `new Test(1)` is assigned to `Test* test`.

Why don't constructors have return values? Because when a constructor executes, it tells the compiler how much space to allocate in memory, initializes member variables, determines the address of `this`, and after doing these things, it doesn't allow the user to customize the return type? Because the return value of the constructor is always and must be itself, it's not transparent to the user (why does the compiler "presumptuously" do this)?

Why don't constructors have return values? I don't know the answer to this question, nor am I very interested in knowing. But this question has given me great inspiration and a lot of shock. The phrase "new member function" is really a novel idea. We take too many things for granted, as a matter of course, thinking it should be that way, but rarely ask why it is that way, and rarely seriously think about and understand many things.
