---
title: Signs of Poor Work Performance
date: 2019-07-25 00:05:30
tags: Work
---

It's around midnight, the time I should be sleeping, but suddenly I feel like writing something.

- **Complaints** 

  Constantly complaining about everything, showing dissatisfaction on their face and voicing it out, afraid that others might not know they have an opinion. :)

- **Inability to Handle Work Pressure**

  Complaints often stem from the work itself. A slightly heavier workload leads to complaints: there's too much work, I'm so annoyed, I can't finish, I can't keep up, I don't have time. In reality, it's just a normal workload, no need for overtime, and everyone enjoys slacking off once in a while. :)

- **Emotional**

  When colleagues raise questions, the common response is to ignore them, and if things get slightly serious, they confront each other. The most surprising reason for anger could be "he doesn't respect my work". It's just a job, what achievements can labor have? :)

- **Self-Centered**

  Cooperation and division of labor in normal work feel like asking the government for help; it needs to be at the right time, convenient, when they have time, and when they're in a good mood. Otherwise, you might see an unpleasant face. I once thought everyone was equal. :)

- **Your Fault, Not Mine**

  Waiting for someone to solve the problem: it's your fault, not mine. This issue needs you to fix it, not me. If there's something, find you instead of me; if you can solve it, don't let me get involved. :)

- **Lack of Professional Skills**

  - **Poor Modularization Ability**

    Function components, module abstraction. A front-end engineer who doesn't understand backend architecture isn't a good coder. Due to a lack of understanding of the entire project structure, the reuse ability is too poor, leading to large workloads when requirements change, which causes the first point. :)

  - **Lack of Programming Skills**

    Can write code but can't program. Lacks basic computer knowledge, appears blind and powerless when encountering functionality, and due to their own limitations, the difficulty of work increases, leading to longer working hours and larger workloads, causing the first point. :)

  - **Lack of Self-Awareness**

    What can you do, what can't you do? :)

  - **Giant in Thought**

    A giant in thought but a dwarf in action. Even if they know they have shortcomings and verbally express or take action to change, the effect is minimal. Generally, the reason for this phenomenon is that they haven't thought it through. :)

- **Blindly Competitive**

  Refuses others' help, hoping others will be more friendly and cooperative in line with their abilities. Cares more about "completing" tasks by themselves rather than "completing tasks".

- **Unclear Goals**

  What is the purpose of work? If it's so painful, why not change industries? :)

- **...**

I have no malice, just describing some objective phenomena. I pity those who lack ability and feel helpless when facing tasks beyond their capability, but I also feel uncomfortable. Working with such people might be a nightmare. I try to avoid points I dislike and warn myself not to become like that. Improve myself, then I can have the qualification to dislike others. It's around 1:05 AM, I need to sleep.

### Update

Nearly a month has passed, and I seem to start understanding some behaviors and phenomena.

Here's the thing, recently, I tried to use C# to develop a simple desktop application. My idea was simple: customize a few shortcut keys to replace multiple backspaces. For example, the global hotkey `Ctrl-5` would act like pressing the backspace key five times, and `Ctrl-6` would act like pressing it six times.

This idea came from my daily typing process. Due to the fast typing speed of both hands, which is like multi-threaded disordered execution, there often appeared misplaced letters in Pinyin, and with quick hands pressing the space key, a long string of incorrect words would appear on the screen. Pressing the backspace key multiple times each time is quite unpleasant, especially when the smooth flow of thoughts is interrupted. It feels like the backspace key not only affects the smooth typing but also brings a sense of having to take responsibility for previous small mistakes with a certain resentment.

Initially, I thought it would be simple: just register a set of global keys with the system and simulate keyboard input. However, during the actual development process, I encountered many difficulties. At first, I couldn't even distinguish between Window and Form in WPF. The tutorials I found on Google mentioned various methods to register shortcuts, but few were truly usable. For simulating keystrokes, I had to rely on search engines because the methods like `Keys.send()` mentioned in official documents didn't work (maybe the event level was too low).

After being able to achieve basic shortcut registration and keystroke simulation, I encountered new problems. The shortcuts were based on `Ctrl`, and pressing the shortcut would execute the program, simulating `Backspace`. However, the Ctrl key was still pressed down, and the system would interpret it as `Ctrl-Backspace`, which was not the desired effect. Then I switched Ctrl to Alt and finally determined that the Shift key combined with Backspace had no conflicts. But a new problem arose: the program-simulated keystrokes triggered at most twice, so the actual effect was at most two backspaces, no matter how many times I looped or changed the nInPints parameter.

So, I'm not saying developing such a thing is difficult, just that I encountered some problems I'm not good at solving. During that process, I experienced emotions like "I'm so annoyed", "This is so hard", "It's really troublesome", "It's not that simple".

Perhaps, we should have more understanding.
