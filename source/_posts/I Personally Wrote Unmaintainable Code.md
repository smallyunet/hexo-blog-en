---
title: I Personally Wrote Unmaintainable Code
date: 2019-12-15 20:03:32
tags: Work
---

Recently, I tried some offline activities and had a few days of severe pain. Now that things have calmed down a bit, it feels like a lifetime ago. There is a project that is undergoing final acceptance, so I pulled out the code from a month or two ago to manually update some data. Looking at the code I wrote not long ago, I felt like it was from a different era. If someone else had written it, I would have immediately thought it was garbage. Since I wrote it myself, objectively speaking, it is indeed garbage.

It was a memorable day when 2019 truly began for me. I used to work with the old system's code, where I had to start the project in resin, which took at least two minutes to compile, making it hard to debug. I used to develop and debug programs on the test server instead of releasing code, and I even encountered pitfalls while writing small tasks like batch-executing SQL. I once looked down on garbage code and developers still using Eclipse in 2019.

To this day, I have become a developer who writes garbage code.

### Minor Requirements

The first time I wrote garbage code, I vividly remember printing logs twice:

```java
catch (e) {
    System.out.println("Exception caught");
    logger.error("Exception caught");
}
```

The number of times logger appears is the number of times println appears. This is clearly an ugly practice. At the time, the nohup.out and log folders were not in the same directory. The log folder was configured to be in a unified directory, and I wanted nohup.out to also output logs... The specific reason is a bit fuzzy now, but this was the first time my own code disgusted me. It seems I removed it in a later version, vaguely remembering it.

In the same requirement, there was a commendable and praiseworthy approach in the old boss's code. At the time, I thought it was redundant and a bit of a challenge to authority. It turns out I was still young.

```java
Can't remember the code T_T
```

In the same requirement, I needed to implement customizable SQL statements, similar to `?` in jdbcTemplate or `#{}` in mybatis. I used a very crude approach:

```java
// Requirement
sql = update set a={a}, b={b}, ...
// Solution
for (i = 0; i < param.num; i++) {
    switch(match.group()) {
        case {a}: break;
        case {b}: break;
        ...
    }
}
```

When asked if the parameter positions could be swapped, I was still confident. Could there be more or fewer parameters?...

### Changing Requirements

I once saw a program with a single class in one file, which is natural. This class had only one method, which was written by a female programmer and had over a thousand lines.

My task was to add a few fields to the originally output report. Initially, it output `a,b`, and now it needs `a,b,c,d`. `c,d` was my part to implement, with logic similar to `a,b`, but from different sources and executed with different SQL queries. Ideally, modifying the SQL should suffice, but the problem was that the method of over a thousand lines had three or four nested loops, involving business operations from different banks, with variables named `list1, list2` inside and outside the loops. The final result involved accumulating `a,b`.

I chose not to touch the original program, copied it, executed it separately, and integrated the data afterward. Arrogant and proud, I didn't want to read the garbage code. So later, one class had two methods with a thousand lines each. The issue was the constant bugs. The copied code still needed to be understood from beginning to end, and integrating the two data parts brought many unexpected problems and vulnerabilities...

### Perfecting Requirements

Another requirement was to take over a project someone else had half-finished. In 2019, someone used a template engine in Spring Boot to write pages, and I had to use jQuery and a jQuery-based mobile style library to complete the development. The interface was ugly, but more importantly, the documentation! The documentation was inconsistent! My mistake was underestimating the requirement. I should have asked when in doubt and understood each aspect before starting. The result was naturally bad, with constant revisions. No one would want to maintain that code.

### Constantly Changing Requirements

Failing to finalize requirements at the start inevitably leads to bad code. Ideally, with proper planning, ample time, and high-quality developers, various designs and patterns are naturally good. Reality is never like that.

In Vue frontend projects, there is a common practice of encapsulating axios, writing various exports in a separate api.js, and importing them in modules. Supposedly for unified API URL management or something, but in practice, it turned out to be cumbersome, causing many development inconveniences. Did it make maintenance easier? Not at all. One reason is the constantly changing requirements. If the comments in the API are not clear or accurate, extracting them separately is meaningless.

```
// api.js
export a = () => { get('a') }

// model.js
import {a} from api.js

a().then(res => {})
```

If the `a` function in the API doesn't correspond exactly to route `a`, modifying api.js requires finding the corresponding function on the respective page. This is no different from writing the route directly on the page. Maybe we could try encapsulating the routing functions at the top of each module or page, making the API in the module or page autonomous.

In frontend projects, CSS is more frustrating than APIs! Even more frustrating is when the project is already mature! The same background color configuration might be written in index, common, model, and even inline styles... And the configuration content differs in each place. Background color styles are fine, but document flow, positioning, floating, and margins are troublesome. True CSS mastery lies in document flow, and if someone who doesn't fully understand it holds up an entire project, who dares to touch that code?

### Requirements Too Lazy to Rewrite

With constantly changing requirements, another phenomenon is reusing old code whenever possible to complete current requirements as quickly as possible. Since future requirements are uncertain, refactoring clean code every time would be labor-intensive and inefficient... This is definitely a path to bad code.

In backend development, there's something I dislike: beans, also known as entities. It's an old practice, including templated DAOs and services. What's the point of writing IClass and then ClassImpl? Beans are not conducive to changing requirements; sometimes, using a map is better. Referring back to the project for acceptance, my garbage code was related to beans. The bean design included many fields. When requirements changed, fields changed too. Modifying it meant changing many places. Who knows if requirements will change again? So, I forcibly reused it, resulting in fields with unclear meanings and some fields completely mismatched, merely occupying space to store data.

Of course, wrong framework choices also contribute to bad code, which is due to my lack of experience. It has to be admitted that architects play a crucial role in project management.

### Development Dilemmas

One dilemma is naming. Occasionally, I wrote the wrong interface name or copied and pasted similar logic, too lazy to change variable names, resulting in unmaintainable code. This shows that code reviews are essential in corporate development, although small companies may not have high code quality requirements. Except for code that goes live and involves online business, reviews are mandatory to avoid disasters. For ordinary development, reviews might not be as strict.

Another dilemma is module division. For instance, if page A has file upload and page B has file download, should file upload and download be in the same API path? It seems logical since they are file operations. But if other APIs are divided by page, with all interfaces of the same page under the same path, is it awkward to single out file operations? Sometimes, modules are divided by data tables, but joint table operations can complicate conventions. Mixing various rules often leads to confusion and unmaintainable code.

### ?

As the year ends, I have to put a question mark on my 2019.
