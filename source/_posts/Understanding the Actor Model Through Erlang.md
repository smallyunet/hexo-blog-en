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

The most surprising finding is that Akka's actors are even faster than Go's goroutines. Below 1,000 interactions, Akka is slower than Erlang. At the 10K scale it overtakes Erlang; at 100K it overtakes Java; and at 1M it overtakes Go and remains in the lead. This is an astonishing result. Although both implementations run on the JVM, Akka takes only about one-third as long as the Java version. Communication between Java threads may indeed introduce substantial overhead.

It is unfortunate that I did not test Elixir. Why Akka is so fast, whether that speed is related to the Actor model, and how strong that relationship is all require further investigation.

(The End)

### Akka

The Akka program used in the benchmark is based on Akka's official Hello World example. The Actor model is readily visible, especially in the `!` operator and the `receive` method.

``` Scala
import akka.actor.typed.ActorRef
import akka.actor.typed.ActorSystem
import akka.actor.typed.Behavior
import akka.actor.typed.scaladsl.Behaviors
import GreeterMain.SayHello
```

These are the imports. If you use an editor such as VS Code, they are important. As in the Erlang program, there is a `Greeter` that sends messages, a `GreeterBot` that receives and replies to them, and a main method.

``` Scala
object Greeter {
  final case class Greet(whom: String, replyTo: ActorRef[Greeted])
  final case class Greeted(whom: String, from: ActorRef[Greet])

  def apply(): Behavior[Greet] =
    Behaviors.receive { (context, message) =>
      message.replyTo ! Greeted(message.whom, context.self)
      Behaviors.same
    }
}
```

This is the message-sending `Greeter`. Calling `Greeter` as a function automatically executes the code in `apply`. The `apply` method uses `receive`, which, like Erlang's `receive`, waits until the actor receives a message. `replyTo` is the GreeterBot's equivalent of a PID. After Greeter receives a message, it sends a reply to GreeterBot.

``` Scala
object GreeterBot {
  var startTime = System.currentTimeMillis()

  def apply(max: Int) = {
    bot(0, max)
  }

  private def bot(greetingCounter: Int, max: Int): Behavior[Greeter.Greeted] =
    Behaviors.receive { (context, message) =>
      val n = greetingCounter + 1
      context.log.info("{}", n)
      if (n >= max) {
        context.log.info("The End | {}", System.currentTimeMillis() - startTime)
        Behaviors.stopped
      } else {
        message.from ! Greeter.Greet(message.whom, context.self)
        bot(n, max)
      }
    }
}
```

This is `GreeterBot`. Compared with Erlang's concise code, Scala's lengthy type declarations can feel cumbersome. After receiving a message from Greeter, GreeterBot checks whether `n` has reached `max`. If enough iterations have run, it stops; otherwise it recursively calls itself.

``` Scala
object GreeterMain {

  final case class SayHello(name: String)

  def apply(): Behavior[SayHello] =
    Behaviors.setup { context =>
      val greeter = context.spawn(Greeter(), "greeter")

      Behaviors.receiveMessage { message =>
        val replyTo = context.spawn(GreeterBot(max = 10), message.name)
        greeter ! Greeter.Greet(message.name, replyTo)
        Behaviors.same
      }
    }
}

object AkkaQuickstart extends App {
  val greeterMain = ActorSystem(GreeterMain(), "AkkaQuickStart")
  greeterMain ! SayHello("Charles")
}
```

Finally, this is the main method, which may also look rather long. A class extending `App` can be run as the main class. It registers `GreeterMain` with the Actor system and executes `GreeterMain.apply`. GreeterMain spawns two processes, behaving similarly to the Erlang program.

### Go

The Go program is much more concise. Here is its header:

``` Go
package main

import(
  "fmt"
  "time"
)

var maxCount = 100000000
var startTime = time.Now().UnixNano() / 1e6
```

It defines two variables: the number of iterations and the program's start time.

``` Go
func main() {
  ch := make(chan bool)
  exit := make(chan bool)

  go func() {
    for i := 0; i < maxCount; i++ {
      fmt.Println(i)
      <- ch
      ch <- true
    }
  }()

  go func() {
    defer func() {
      timeUsed := time.Now().UnixNano() / 1e6 - startTime
      fmt.Println("The End | ", timeUsed)
      close(ch)
      close(exit)
    }()
    for i := 0; i < maxCount; i++ {
      ch <- true
      <- ch
    }
  }()

  <- exit
}
```

The two goroutines alternate between reading from and writing to a channel. The Go program looks much cleaner; the Scala version is visually exhausting by comparison.

### Java

Java is no less verbose than Scala.

``` Java
public class Test{
    public static void main(String[] args) {
        Object lock = new Object();
        Thread sender = new Sender(lock);
        Thread receiver = new Receiver(lock);
        sender.start();
        receiver.start();
    }
}
```

The main method starts two threads that share a lock.

``` Java
class Message {
    static long MAX_COUNT = 100000000;
    static String status = new String("init");
    static long count = 0;
    static long startTime = 0;
    public static void send() {
        System.out.println(count);
        status = "sent";
        count++;
        if (count == 1) {
            startTime = System.currentTimeMillis();
        }
        if (count >= MAX_COUNT) {
            status = "stop";
            long time = System.currentTimeMillis() - startTime;
            System.out.println("The End | " + time);
        }
    }
    public static void receive() {
        status = "received";
    }
    public static String getStatus() {
        return status;
    }
}
```

`Message` is the shared resource that stores the message state. It also performs a few auxiliary actions when the state changes, printing the required logs.

``` Java
class Sender extends Thread {
    Object lock = null;
    public Sender(Object lock) {
        this.lock = lock;
    }
    @Override
    public void run() {
        while (!Message.getStatus().equals("stop")) {
            synchronized (lock) {
                if (Message.getStatus().equals("init")
                  || Message.getStatus().equals("received")) {
                    Message.send();
                    lock.notify();
                    try {
                        lock.wait();
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }
            }
        }
    }
}

class Receiver extends Thread {
    Object lock = null;
    public Receiver(Object lock) {
        this.lock = lock;
    }
    @Override
    public void run() {
        while (!Message.getStatus().equals("stop")) {
            synchronized (lock) {
                if (Message.getStatus().equals("sent")) {
                    Message.receive();
                    lock.notify();
                    try {
                        lock.wait();
                    } catch (Exception e) {
                        e.printStackTrace();
                    }
                }
            }
        }
    }
}
```

`Sender` and `Receiver` are similar. Sender sends a message and then waits for Receiver's response. Receiver continually checks in a loop whether a message has arrived. When it has, Receiver replies, wakes Sender to tell it that the message is ready to process, and then waits for Sender's next response.
