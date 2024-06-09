---
title: Understanding the Actor Model Through Erlang
date: 2020-03-31 12:47:59
tags: Programming Languages
---

The Actor Model is a broad concept that was proposed in the last century. It views an actor as a whole, which can be an atomic variable, an entity, or a thread. Actors communicate with each other, and each actor has its own state. Upon receiving messages from other actors, it can change its state or perform other actions. When discussing the Actor Model, examples such as Erlang, Elixir, or Akka are often cited as they all implement the Actor Model to some extent.

Front-end MVVM frameworks like React and Vue have their own data flow management frameworks, such as Redux and Vuex. These frameworks include concepts like Action, Reducer, and State, which can sometimes be confusing. As front-end development becomes more complex, some elements may be borrowed from the back-end, like TypeScript's type system. I am curious if there is a conceptual similarity between actions in these front-end frameworks and the Actor Model in the back-end.

In essence, actions are simple, and the underlying code is also simple. In a reducer, different operation types are judged using switch, and different methods are called. The simplest form is a method action that changes the value of the global variable state. The Redux documentation states that its design is derived from the Flux architecture. The origin of the Flux architecture is unclear, but it is unlikely to be inspired by the Actor Model.

``` JavaScript
let state = null
function action(val) {
  state = val
}
```

Erlang is an old programming language and is a typical language inspired by the Actor Model. Understanding concepts in isolation can be abstract, so starting with a specific, concrete language might help explore these theories. For example, learning FP through Haskell is much more effective than through Java. Elixir is a language based on the Erlang VM, and its relationship with Erlang is similar to that of Scala and Java, making Erlang's syntax relatively simple and clean.

### Erlang

Erlang code blocks end with `.`. A code block can be a single line or multiple lines, where `.` functions similarly to `}`, as Erlang does not have `{`. Statements within the code block end with `,`, indicating the end of a statement, akin to `;` in some languages.

Erlang defines a program file as a module, and the module can be loaded in the command line using `c(test).`. The module name must match the file name:

``` Erlang
-module(test).
```

The header of the file needs to define the functions exported by the program, which are the module's entry points:

``` Erlang
-export([start/0, ping/3, pong/0]).
```

Here, three functions are exported. Brackets indicate an array, and the numbers `/0` and `/3` indicate the number of parameters for each function. The start function serves as the main entry point of the program, responsible for starting the entire program. The ping function sends messages, and the pong function receives messages and responds.

Erlang has a concept of `process`, which is neither a thread nor a computer-level process; it is just a `process`. We will start two processes: one for ping and one for pong, simulating message transmission and interaction. This can be likened to starting two threads, one for production and one for consumption.

``` Erlang
ping(0, Pong_PID, StartTime) -> 
    Pong_PID ! {finished, StartTime};
```

This is the first part of the ping function, a branch that accepts three parameters. If the first parameter is 0, the statements within this function will be executed. The second parameter, `Pong_PID`, refers to the process containing pong, and the third parameter is the program's start time, used to record the program's runtime. The function body has a single statement: `!` sends a message, meaning the data `{finished, StartTime}` is sent to the process with ID `Pong_PID`, where finished is an `Atom` that acts as an identifier sent to pong. An Atom is one of Erlang's data types, akin to an undeclared constant.

``` Erlang
ping(N, Pong_PID, StartTime) -> 
    Pong_PID ! {ping, self()},
    receive
        pong -> 
            io:format("~w~n", [N])
    end,
    ping(N - 1, Pong_PID, StartTime).
```

This is the second part of the ping function. If the first parameter received by the function is not 0, the statements within this function will be executed. This part of the function sends the data `{ping, self()}` to pong upon receiving the request, where the identifier is `ping` instead of `finished`. The second parameter, `self()`, returns the current process's ID, which is passed to pong for pong to reply to the message. Pong will selectively use the second parameter.

After sending the data to pong, there is a `receive ... end` code block that blocks the current program's execution until the current process receives data. This block performs a simple pattern matching, where `pong` is an Atom variable. If the pong identifier is received, the statement after `->` will be executed. `io:format` is a simple formatted output that prints the value of N to the screen.

After receive ends, ping calls itself recursively until N is 0, meaning the interaction between ping and pong continues N times, and the number of interactions is printed by `io:format`. These are the two branches of the ping function. The pong function is similar to the ping function:

``` Erlang
pong() ->
    receive
        {finished, StartTime} -> 
            io:format("The End");
            io:format("~w~n", [erlang:timestamp()]);
            io:format("~w~n", [StartTime]);
        {ping, Ping_PID} ->
            Ping_PID ! pong,
            pong()
    end.
```

The pong function does not have branches in terms of parameters, but there are two matches within the receive block. If the finished identifier is received, the start and end times are printed, and the program ends. If the ping identifier is received, a pong response is sent to Ping_PID, which is the ping process, and then pong calls itself. This means pong first sends a message and then waits for a reply. If no reply is received, it waits indefinitely.

``` Erlang
start() ->
    Pong_PID = spawn(test, pong, []),
    spawn(test, ping, [10, Pong_PID, erlang:timestamp()]).
```

Finally, the start function is the program's entry function, spawning two processes that run independently. When the first parameter passed to ping is 10, the interaction between ping and pong continues 10 times.

### Interaction Speed

I once heard a so-called "expert" say that to improve computer speed, we should focus on CPU utilization because actors are fast. Why are they fast? Because an actor is a whole that runs on a single core, eliminating the need for inter-core communication. The accuracy of this statement may be debatable, but I am curious if actors are truly fast, which led me to test the speed of actors.

It must be noted that I am well aware this testing method is unreliable.

In the Erlang program, two processes communicate with each other, testing communication times of different magnitudes and recording the program's execution time. For comparison, two threads are started in Java, using thread sleep and wake-up for inter-thread communication. Similarly, two goroutines communicate in Go. As for Akka, which also represents the Actor model, a test program was also written. The table below shows the test results, with the number of times ranging from 1 to 100 million, and the time unit is milliseconds.

| Number of Times | Erlang | Java | Go | Akka |
| --------------- | -----: | ---: | -: | ---: |
| 1               |      0 |    0 |  0 |    3 |
| 10              |      0 |    1 |  0 |    7 |
| 100             |      3 |    4 |  1 |   17 |
| 1,000           |     26 |   30 |  4 |   83 |
| 10,000          |    610 |  168 | 42 |  225 |
| 100,000         |  2,783 |1,295 |404 |  674 |
| 1,000,000       | 27,085 |11,300|4489| 3515 |
| 10,000,000      |273,912 |107,673|40335|29368|
| 100,000,000     |2,851,680|1,092,879|482196|300228|

Initially, I tried rendering this data using Echarts for better comparison, but the resulting line chart was not user-friendly.

Overall, Erlang is the slowest, possibly due to its age and lack of optimization. Elixir might perform better. Comparatively, Java is faster than Erlang, and Go is faster than Java, which seems expected. Java's time consumption is one-third of Erlang's, and Go's is half of Java's.

The most surprising finding is that Akka's actor speed is faster than Go's goroutines. Before 1,000 interactions, Akka is slower than Erlang. At the 10K scale, it surpasses Erlang, at 100K, it surpasses Java, and at 1M, it surpasses
