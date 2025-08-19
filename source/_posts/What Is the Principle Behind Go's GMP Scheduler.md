---
title: What Is the Principle Behind Go's GMP Scheduler
date: 2025-08-18 21:50:50
draft_date: 2025-08-18 19:25:50
tags: 
- Go
- Programming Language
---

**Disclaimer**: I look down on technical topics like “What is the principle behind Go's GMP scheduler?”

I'm usually not interested in studying this type of question. But it's been asked so frequently in interviews that I'm now setting aside 2 hours to understand it. On one hand, I want to see if there's any real technical depth behind it; on the other hand, I'm writing down the answer here. But I won’t let this information stay in my brain—next time I'm asked in an interview, I’ll still say I don’t know 😏

### Basic Concepts

GMP is an acronym:

- **G (Goroutine)**: A coroutine; every `go` statement adds a new G.
- **M (Machine)**: A system-level thread, similar to a thread in other languages.
- **P (Processor)**: The number of P equals `GOMAXPROCS`, typically the number of CPU cores.

GMP means: spin up several M (threads) to execute G (goroutines), but at most P (cores) M’s can run in parallel.

### Three Invariants

Here come some boring (and simplified) rules:

1. Only M's that obtain a P can execute tasks.
2. Runnable G’s reside in either a P’s local run queue or the global queue.
3. When an M becomes blocked (syscall/cgo), it promptly hands over its P.

These may sound confusing now, but they’ll make more sense with the code examples later.

### GMP Debug Log

Here’s a basic code file to demonstrate spawning a goroutine:

```go
package main

import (
	"fmt"
	"sync"
)

func main() {
	var wg sync.WaitGroup
	wg.Add(1)
	go func() {
		defer wg.Done()
		fmt.Println("Hello from goroutine")
	}()
	wg.Wait()
}
```

Run it with debug flags:

```bash
go build demo0.go
GODEBUG='schedtrace=200,scheddetail=1' ./demo0
```

Don’t use `go run`—it introduces extra runtime logs. The binary version’s logs are cleaner:

```bash
SCHED 0ms: gomaxprocs=10 idleprocs=7 threads=5 spinningthreads=1 needspinning=0 idlethreads=0 runqueue=0 gcwaiting=false nmidlelocked=1 stopwait=0 sysmonwait=false
  P0: status=0 schedtick=0 syscalltick=0 m=nil runqsize=0 gfreecnt=0 timerslen=0
  P1: status=1 schedtick=0 syscalltick=0 m=2 runqsize=0 gfreecnt=0 timerslen=0
  ...
Hello from goroutine
```

These logs show:

- The first line starting with `SCHED` is a summary—10 P’s were launched (`gomaxprocs=10`).
- Only `P1` is running, held by `M2`.
- `P0` is held by `M3` and is in `spinning` state (waiting for tasks).

You don’t see the print-related G because it finishes too quickly—GMP details can be viewed via debug logs like this.

### Preemptive Scheduling

```go
package main

import (
	"fmt"
	"runtime"
	"time"
)

func busy(tag string, d time.Duration) {
	end := time.Now().Add(d)
	x := 0
	for time.Now().Before(end) {
		x++
	}
	fmt.Println(tag, "done", x)
}

func main() {
	runtime.GOMAXPROCS(1)
	go busy("A", 1500*time.Millisecond)
	busy("B", 1500*time.Millisecond)
}
```

Sometimes "A" prints first, sometimes "B".

We set `GOMAXPROCS(1)`, so only one P exists. Yet, Go's GMP scheduler preempts every 10ms, meaning even if `go busy("A")` is running, it eventually yields, letting the main thread run `B`.

To visualize this more clearly:

```go
func busy(tag string, d time.Duration) {
	end := time.Now().Add(d)
	next := time.Now()
	for time.Now().Before(end) {
		if time.Now().After(next) {
			fmt.Print(tag, " ") // Print ~every 100ms
			next = time.Now().Add(100 * time.Millisecond)
		}
	}
	fmt.Println(tag, "done")
}
```

Sample output: `B A B A B A A B A B A B A B A B A B A B A B A B B A B A B A B done`

This confirms that `A` and `B` alternate—they’re interleaved due to GMP's preemptive scheduler.

### P Work Stealing

```go
package main

import (
	"runtime"
	"sync"
	"time"
)

func spin(d time.Duration) {
	deadline := time.Now().Add(d)
	for time.Now().Before(deadline) {
	}
}

func main() {
	runtime.GOMAXPROCS(1)

	const N = 120
	var wg sync.WaitGroup
	wg.Add(N)
	for i := 0; i < N; i++ {
		go func() { defer wg.Done(); spin(500 * time.Millisecond) }()
	}

	time.Sleep(30 * time.Millisecond)

	runtime.GOMAXPROCS(4)
	wg.Wait()
}
```

Initially all G's go to one P. Then `GOMAXPROCS` is increased to 4—new P’s (P1–P3) "steal" tasks from P0’s queue.

Debug logs show:

```bash
P0: runqsize=17
P1: runqsize=5
P2: runqsize=5
P3: runqsize=17
```

Initially, all G’s were with P0. Later, others came and took some.

### P's runq and the Global Queue

```go
package main

import (
	"runtime"
	"sync"
	"time"
)

func spin(d time.Duration) {
	end := time.Now().Add(d)
	for time.Now().Before(end) {
	}
}

func main() {
	runtime.GOMAXPROCS(1)

	const N = 600
	var wg sync.WaitGroup
	wg.Add(N)
	for i := 0; i < N; i++ {
		go func() { defer wg.Done(); spin(800 * time.Millisecond) }()
	}

	time.Sleep(500 * time.Millisecond)

	runtime.GOMAXPROCS(4)
	wg.Wait()
}
```

Use debug to analyze:

```bash
go build demo4.go   
GODEBUG='schedtrace=200,scheddetail=1' ./demo4 &> demo4.log
```

Initial logs show:

```bash
runqueue=0 // global queue
P0: runqsize=0
P1: runqsize=0
```

Later, P0 starts many G’s:

```bash
P0: runqsize=204
runqueue=395 // global queue overflow
```

Default `runq` capacity is 256. Excess tasks go to the global queue. When P1–P3 enter, they pick tasks from the global queue first—not by stealing.

If a P finds no work in its runq or the global queue, it checks netpoll (OS), and if that fails, it spins.

### Blocking syscalls Yield P

```go
package main

import (
	"fmt"
	"runtime"
	"time"
)

func main() {
	runtime.GOMAXPROCS(2)

	go func() {
		time.Sleep(2 * time.Second)
		fmt.Println("blocking done")
	}()

	go func() {
		for i := 0; i < 6; i++ {
			time.Sleep(300 * time.Millisecond)
			fmt.Println("still running", i)
		}
	}()

	time.Sleep(3 * time.Second)
}
```

Output:

```bash
still running 0
still running 1
...
blocking done
```

Even though the first G blocks, the second keeps running—GMP properly yields P when blocking occurs.

### Disable Async Preemption

```go
package main

import (
	"fmt"
	"runtime"
	"time"
)

func spin() {
	for { }
}

func main() {
	runtime.GOMAXPROCS(1)
	go spin()
	time.Sleep(100 * time.Millisecond)
	fmt.Println("I should still print unless preemption is off")
}
```

Two ways to run:

```bash
go build demo7.go
GODEBUG='schedtrace=1000,scheddetail=1' ./demo7
```

and:

```bash
go build demo7.go
GODEBUG='schedtrace=1000,scheddetail=1,asyncpreemptoff=1' ./demo7
```

With `asyncpreemptoff=1`, async preemption is disabled—the print never happens due to infinite loop. This illustrates GMP's ability to yield CPU, and what happens when it’s turned off.

### Go Source Code

I didn’t dig deeply into the source. For example, constants for G/M/P are in [`src/runtime/runtime2.go`](https://github.com/golang/go/blob/master/src/runtime/runtime2.go#L18):

<img src="1.png" width="60%">

And `runqputslow` in [`src/runtime/proc.go`](https://github.com/golang/go/blob/master/src/runtime/proc.go) handles queue overflow:

<img src="2.png" width="60%">

### Digging Deeper

This post is probably incomplete—I’m not going any deeper. Some people may love diving into this.

GMP is an engineering implementation of a coroutine scheduler. Many care about the engineering details—task queue management, preemption, yielding, etc. But underneath, all coroutine schedulers are based on continuation. Go just happens to spotlight coroutines. Other languages can implement their own coroutine schedulers too.

So here’s the question: if you love researching GMP, have you studied coroutine/virtual thread/async function/process implementations in other languages? How do they differ from Go’s goroutines?

If my job someday requires a deep understanding of these, I’ll learn them.

### Doubts

I once said:

Go designed automatic garbage collection to reduce the mental load of memory management. Yet some interviewers go out of their way to master GC internals and quiz candidates on it. If you truly believe in using your brain to manage memory, why not just use Rust?

It’s like I’m learning to drive, and someone expects me to understand how internal combustion works before getting a license. I’m not building cars—I’m driving one.

Same goes for Go’s `go` keyword. It was built to make concurrency easy. Yet some folks study its internals obsessively, and use that as a gauge of Go proficiency. Doesn’t that contradict the language's intent? If a language needs you to understand scheduling internals to write good code, it has failed in its abstraction.

If you’re building programming languages or implementing schedulers, by all means, understand GMP thoroughly. Otherwise?
