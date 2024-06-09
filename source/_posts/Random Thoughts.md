---
title: Random Thoughts
tags:
  - Work
date: 2021-11-23 23:40:59
draft_date: 2021-11-23 00:43:21
---

It's almost 1 AM and I can't sleep, so I'll write a little. Today during the day (yesterday), the tester reminded me that REST API input parameters should start with uppercase letters.

At first, I was a bit nervous. Did I fall behind? Have I not been developing according to the standards? REST API parameters with uppercase initials? (The interface's `Content-Type` is `application/json`).

Then I checked GitHub's API documentation, took another look at the DIDs (Decentralized Identifiers) standard document, and combined it with my long experience using JSON. No, there's no habit of starting JSON keys with uppercase letters.

Then I thought of a ridiculous reason and asked, "Is the uppercase initial parameter a company standard?" The tester replied:

> I asked before, and it seems like you developers write it that way yourselves, like whether it's a public or private variable, it should start with an uppercase letter.

Sure enough. (smile)

### Access Control

Access modifiers in Java are a basic concept used to control class access permissions. There are 3 keywords and 4 situations, such as `public` for public access and `private` for private access:

``` Java
public class aaa {
    public int a;
    private String b;
}
```

For more details, refer to Java's documentation: [Controlling Access to Members of a Class](https://docs.oracle.com/javase/tutorial/java/javaOO/accesscontrol.html).

Languages like Python, which are scripting languages, don't have access control concepts. So, it's conventionally accepted that variables named with a leading `_` are private, although there's no code-level protection, just a naming difference.

```
class aaa:
    a = 1
    _b = 2
```

In Python, variable names starting with `__` can achieve code-level private variable effects, but using `__` for private variables isn't entirely Python's intention. Refer to Python's documentation: [9.6. Private Variables](https://docs.python.org/3/tutorial/classes.html#private-variables).

It's well known that Golang is a peculiar language, not only requiring you to write braces on the same line as the function name but also requiring you to do some things, like variables starting with an uppercase letter indicating public and those with a lowercase letter indicating private.

Golang's distinction between variable case isn't just a naming difference but a code-level control. What will the following code output?

```
func main() {
	a := struct {
		i int
	}{
		i: 1,
	}
	s, _ := json.Marshal(a)
	fmt.Println(string(s))
}
```

Of course, it's `{}`, because `i` is lowercase and is automatically ignored during marshaling.

This is actually quite an unusual requirement. Golang reduces keywords and reuses keywords for "simplicity" in design, but it still needs to provide certain capabilities, requiring some odd workarounds.

In the above Java code, I deliberately used a lowercase `aaa` for the class name. Java classes generally start with an uppercase letter, but it doesn't restrict you from writing your code in any particular way.

Even though I work with Golang, I'm not its disciple, nor am I a disciple of any other language. If you're still a disciple of a particular language, I hope you can change your perspective soon.

So, because of some "features" of Golang, to make serialization a bit easier, the project's REST API tends to use uppercase initial variable names as keys.

Time to sleep, I'll write more tomorrow.

### No Issues

There's no issue whether JSON keys start with uppercase or lowercase letters. To save the trouble of writing `<code>json</code>`, the output parameters are uppercase by default, so the input parameters are uppercase as well. This is an engineering symmetry and poses no problem.

I remember once designing a REST API where input parameters used camelCase `myName` and output parameters used snake_case `my_name`. The reason was that Java's Entity could automatically map camelCase variables in the POST body to the object during input. For output parameters, the program directly returned data from the database, which was in snake_case (not following the development standard). This achieved the simplest code but indeed had asymmetric input and output, which was inappropriate.

### One Thing

If it was just this issue, it wouldn't be worth writing about. Yesterday, when the tester told me how something should be done, I thought of another incident that happened at a previous company.

There are two forms of URL input parameters: one is `.com/?a=1&b=2`, and the other is `.com/:a/:b`. In that project, it was entirely the second form, i.e., route-form parameters.

The second form was sometimes particularly long, like `.com/:a/:b/:c/:d/:e`. Parameters were often query conditions, and if a parameter in the middle wasn't needed as a condition, i.e., no need to pass the parameter, this route-form parameter couldn't skip a segment. They used a bizarre workaround: `all`. Use `all` to replace the unneeded parameter, `.com/:a/:b/all/:d/:e`. The program's judgment was also quite ridiculous.

REST interfaces are a kind of web service, but if you lack basic web development knowledge, it’s easy to make shortcomings.

At that time, I suspected the technical level of the person who developed that project, my nominal team leader. Well, maybe it was an unpleasant memory.

To explain, I don't just have opinions about others for no reason. At that company, there was a performance system where each quarter you had to write a performance plan about what you would do. Generally, the team leader set a big goal, and the team members divided it and completed various tasks. Because I doubted his ability, I feared he would set low goals, limiting my potential, so I tested him. Having opinions about others doesn't benefit me; I was just afraid I'd be in a position where I couldn't perform well.

I didn't intend to resign, so I was trying hard to improve my environment and situation to get a situation I could adapt to. I remember when colleagues resigned, I joked that the happiest time in work might be when you submit your resignation and receive an offer. A colleague said the happiest time was when the year-end bonus was given… also a joke. They then said if it wasn't desperate, no one would want to resign.

I knew parameter form issues were minor, but I insisted the interface design should use parameter form instead of route form. Parameter form is better, so why not use it? Explain your reason; besides "previous people wrote it this way," do you have any technical considerations? Can you overwhelm me technically, showing your skills in detail?

My basic view was he lacked web development experience and didn't know about parameter forms. I analyzed two possibilities: either he had skills but didn't say, or he lacked skills. So I created a topic from a small issue, giving him a chance to speak, and later expressed my doubts about his abilities to the leader strongly. I thought if he had skills, he should show them.

Sigh, what followed were some closure steps.

### Empathy

I also thought, if I were him, what would I do?

If a junior treated me that way, could I handle it better?

If someone questioned my technical skills or focused on my mistakes, what would I do?

I knew I seemed to disrupt a harmonious peace.

### And Then

Now, I have no interest in arguing over such small issues. What’s right or wrong, who cares?

But past experiences seem necessary. If I hadn't been serious in the past, I might still be curious: what would happen if I insisted on my views or argued over right and wrong?

Because of past experiences, I know the results.

The results aren't great, and a bit boring.

I don't like or expect that.

No more for now.
