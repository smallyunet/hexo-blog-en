---
title: People Who Hype Cursor Usually Lack Technical Skills
date: 2025-04-12 20:33:38
tags: AI
---

The title is a bit clickbaity—don’t take it too seriously. What I mean is that we shouldn't focus too much on the tools we use. It’s simple logic: only when your own engineering skills fall short of what Cursor can do would you think Cursor is amazing and can do everything. Of course, that’s kind of an empty statement.

In fact, Cursor’s code completion isn’t much better than GitHub Copilot’s. They both use the same underlying models—either Claude or GPT. In terms of engineering fine-tuning, both Cursor and Copilot work by using some files as context for ongoing conversation.

Cursor is slightly better than Copilot in that it actively searches and references other files in the current project directory. That’s genuinely useful—you don’t have to tell it which files to reference; it tries on its own. Copilot, as a plugin, lacks this ability. That’s definitely a strong point for Cursor. If your project has a lot of reusable code, Cursor can really shine.

But sometimes Cursor feels overly “smart.” It even creates new files in your project without your consent, which means you have to keep an eye on whether it’s modifying files you didn’t intend to change. In contrast, Copilot (in GoLand) is just a plugin that offers code suggestions—you choose whether to use them. Copilot in VS Code now offers a similar experience to Cursor, where it gives you the option to accept or reject changes.

So in comparison: Cursor and Copilot use the same large language models (nobody really thinks Cursor trained its own model, right?), but Cursor has more control at the project level, while Copilot, true to its name, acts more like an assistant. That’s their biggest difference.

Now back to the models themselves. o1 is the best model so far—at least from my daily experience. I often use o1 to precisely pinpoint bugs in my code, while other models tend to give incorrect or vague explanations for the same issue, including o3-mini-high and 4o.

For example, when calling a smart contract, the original command was:

===
cast call requestPrice(string) "BTC" —-rpc-url=http://eth:8545
===

Later, I needed to call another function that takes one more argument, so I copied the code and changed it to:

===
cast call requestPrice(string) "BTC" "USD" —-rpc-url=http://eth:8545
===

Did you notice the issue right away? Actually, 4o did give a correct analysis, just not precise—it listed three possible causes. But o1 got it right straight away.

Of course, differences between models aren’t always this clear-cut—just happened to have this example in mind. What I want to say is, from daily use, o1 is the most reliable model.

I seriously suspect that the people hyping up Cursor are those who haven’t really used AI before, and only realized how powerful generative AI has become after using Cursor—so they’re thrilled. And when they need generative AI, their first reaction isn’t to open ChatGPT or Gotk3, but to jump into Cursor’s code panel and start chatting.

So in reality, those hyping Cursor are mistaking the power of generative AI for the power of Cursor itself, which is why they think Cursor is exceptionally good.

I’ve been strongly recommended Cursor more than once at work, even told to stop using GoLand, with people saying things like “GoLand is trash” and “Cursor is the best.”

First of all, I’ve always thought that choice of editor is a personal preference—why make a big deal out of it? Second, if people think your choice of editor reflects a hierarchy or sense of superiority, that’s just childish. Lastly, I use GoLand for two simple reasons: 1) the arrows clearly show interface implementation relationships; 2) the forward/backward navigation shortcuts are very handy. These two reasons help me quickly understand and follow the code.

This leads to a difference: people who use GoLand tend to focus more on code logic. When looking for specific parts of code, they prefer jumping through code relationships like interface implementations. On the other hand, Cursor (VS Code) users focus more on the project’s directory structure, filenames, and file locations—because Cursor lacks solid code navigation features, they have to rely on the structure to piece things together.

As for code autocompletion, GoLand with the Copilot plugin handles everyday needs just fine. Most of the time, what needs completing is something like a log statement. For more complex, logic-heavy code, I’m hesitant to rely on AI.

Maybe the reason some people recommend Cursor to me is they assume I don’t know how to use AI, or think I don’t know how to use Cursor (?). That’s odd. I tried ChatGPT back in 2023 when it first blew up and even wrote [Don’t Underestimate ChatGPT](/2023/03/30/不要小瞧-ChatGPT/). Nowadays, I use ChatGPT all the time—both for work and in life.

All in all, my point is: it’s totally fine to like Cursor or to use Cursor. Just don’t hype Cursor too much.
