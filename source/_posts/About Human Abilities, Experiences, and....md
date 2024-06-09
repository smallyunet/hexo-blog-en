---
title: About Human Abilities, Experiences, and...
date: 2020-05-03 21:50:15
tags: Learning
---

Lately, there doesn't seem to be anything particularly special going on. The amount of work has increased compared to before, occupying a lot of my time. In the gaps outside of work, it seems I don't have much energy to focus on learning.

The content on this blog roughly falls into two or three categories: one is technical stuff I've thought about or learned, another is work and life-related things that I've encountered or thought about which sparked some insights, and the third category might just be daily logs, fantasies, and pure records of what happened. Ideally, the frequency of blog updates would be about every two weeks to a month; too short a time frame leaves little to say, and too long feels a bit too long. If there's nothing to write or I don't feel like writing for a long stretch, it means something dangerous has happened—either I've become lax in thinking or there are abnormal psychological issues. So, the blog can also serve as a mirror for self-reflection.

Lately, I've been struggling with a point that is difficult to conclude.

(1)

A while back on a weekend, a grad school friend asked me a basic operational question. It was about a C++ project cloned from Github that threw errors when opened in VS2012, saying it couldn't find files. I rarely touch C++, but I quickly looked into it. The project is called [wxCharts](https://www.wxishiko.com/wxCharts/), a chart UI library, and almost none of the header files could be found during compilation. His professor told him to add external dependency libraries in the settings, but even after doing so, the header files were still missing.

I suggested picking one of the missing files and searching for it on the disk, but no results...

Later, I checked wxCharts' official website and found out it relies on [wxWidgets](https://www.wxwidgets.org/). wxWidgets is a GUI library, and wxCharts is just a component of this GUI library. After testing locally, I found that compiling and installing wxWidgets allowed wxCharts to compile successfully.

The cause was found, but he said he had already installed wxWidgets. I wasn't sure if the `build` in Windows VS was the same as `make install` in Linux, so I suggested a few options: one, copy the wx folder under wxWidgets to wxCharts (the wx folder is the default dependency folder for the project, similar to the src directory in a Go project); two, try placing the wxCharts source code into the wxWidgets project and see if it compiles; three, reinstall wxWidgets, making sure to install it globally and configure it in the environment variables.

After some fuss, we finally managed to locate the header files. There was an issue with the path configuration for external dependencies, using the wrong environment variable as the path, and redundantly referencing incorrect paths. But compilation still failed, as certain files couldn't be found. More tweaking revealed that besides the header files, dynamic dependency libraries also needed to be included, and the directories for the production and debug versions were different, with the debug version having a 'd' suffix...

The problem we faced was straightforward—missing project dependencies. If it had been a Java project, it would have been a piece of cake (although even some colleagues with years of experience sometimes struggle with such low-level issues). This friend is someone I get along well with; we used to play "Honor of Kings" together in college. I have a bit of an understanding of his abilities—he might be strong academically but is almost a complete novice in computer science. Yet, academically, his undergraduate performance wasn't as good as mine. It was his sincere effort during the year or so of grad school prep that made the difference.

A minor realization I had was that he's now a grad student at a fairly decent 211 university, while my degree is still from a non-prestigious undergrad institution. Our educational levels differ significantly. As for abilities, I can only say that once there was an opportunity to go to grad school in front of me, I didn't cherish it. Even after losing it, I didn't feel a tinge of regret. If I could go back, I'd still make the same decision. If I had to put a time frame on it, I'd hope it's in my lifetime.

(2)

I once mentioned a colleague who constantly perplexes me with their practical skills and sense of responsibility. Yet, after encountering a few issues, I realized they indeed have rich development experience that's hard to refute.

We faced a rather simple recursion problem. I hadn't figured out whether an object passed into a function is passed by value or reference.

```JavaScript
function a() {
    obj = { "a": 1 }
    b(obj)
    console.log(obj)
}
function b(obj) {
    // Method 1
    obj = 1
    // Method 2
    obj = { "a": 2 }
    // Method 3
    obj.a = 2
}
```

I had to clarify this to use recursion correctly. Method 1 assigns a new value to the object, Method 2 assigns a new object to the parameter, and Method 3 changes the object's property. At the time, I had no concept and wondered if it could be solved using the prototype chain. I knew she wasn't clear on this either, but she tentatively mentioned whether it was related to the stack and heap, then dropped it uncertainly.

I thought it was unrelated; I thought she was just guessing. I was wrong.

Objects are passed by reference, no doubt about that. However, whether the object's properties change depends on whether the change is in the stack space or heap space. Assigning a new value to an object, whether simple or complex, will point the object's variable to a new stack address, so the object's properties won't change. Using Method 3 changes the contents of the heap the object points to, thus changing the original object's properties.

So, in fact, experience is useful and difficult to surpass easily, as experience cannot be gained through shortcuts.

(3)

Abilities are acquired through experience. Strong abilities can help acquire more advanced experiences more quickly and efficiently, which in turn strengthens abilities.

Perhaps abilities and experience are complementary, similar to the so-called "experience and insight." Here, "abilities" indeed have a hint of "insight." Sometimes during recruitment, it feels like the recruiters emphasize years of experience, as if hiring more experienced people is a bargain compared to those with less experience. But, regarding abilities and experience...

Recently, I downloaded a music composition software and tried it out. Composing music is actually very challenging, with skill requirements vastly different from programming despite the similar term. Now, with various training courses and online classes, programming has virtually no entry barrier. As we enter an era of "programming for everyone," how will we face this world? Future programming might not permeate life with MVC, ORM, etc., but an IoT device, automated equipment, or future versions of iOS Shortcuts might still need a bit of "programming" logic to better suit human personalized living habits.

PS:

<img src="chortcut.png" width="30%" />

<center>iOS 13's Shortcuts already support basic statement logic</center>
