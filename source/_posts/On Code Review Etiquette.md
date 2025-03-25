---
title: On Code Review Etiquette
tags:
  - Programmer
date: 2025-03-25 21:45:43
draft_date: 2025-03-25 11:45:05
---


Previously, I recommended two blog posts by Yin Wang:

- “[How to Respect a Programmer](https://www.yinwang.org/blog-cn/2015/03/03/how-to-respect-a-programmer)”
- “[Git Etiquette](https://www.yinwang.org/blog-cn/2015/03/11/git-etiquette)”

Due to a lack of standardized practices in my past work experiences, I didn’t have a proper understanding of Code Review. Recently, triggered by a colleague’s emotional remarks on this topic, I started to pay more attention to the issue of Code Review.

### Basic Etiquette: Don’t Use FYI

Google’s published Code Review guidelines, “[The Standard of Code Review](https://google.github.io/eng-practices/review/reviewer/standard.html),” are extremely instructive and comprehensive. Even the process-related issues I currently encounter can be addressed by following this guideline. Of course, this requires that all team members have first agreed upon and understood this guideline, instead of assuming everyone in the company already knows and follows it.

The guideline (TSCR) already covers process issues and etiquette, so I won’t repeat them here. What caught my attention is a small section: “[Label comment severity](https://google.github.io/eng-practices/review/reviewer/comments.html#label-comment-severity),” which talks about categorizing the importance of comments in Code Review, using prefixes so the author can clearly understand which comments must be addressed and which are optional.

Other than mandatory comments (which don't require a prefix), Google's guideline lists three types of prefixes — all from the reviewer’s perspective and none of them block approval or merging:

- `Nit` (Nitpick): You should change it, but I can accept it if you don’t.
- `Optional`: Just a suggestion, feel free to decide.
- `FYI` (For Your Information): No changes required for this PR, but I find this point interesting — something you can look into later.

Every time I see "For Your Information," I instinctively translate it into a common phrase from traditional Chinese culture: “It's for your own good.”

Anyone who grew up in Chinese society — even without harsh parenting — likely understands the weight of this phrase.

For your own good, you must study hard  
For your own good, you can’t play games  
For your own good, you must take the civil service exam  
For your own good, you should get married early  
For your own good, you have to have children  
…

Sometimes, parents use “for your own good” as an excuse to satisfy their desire to control. Other times, they may sincerely mean well but offer poor advice due to limited vision.

In short, “for your own good” carries a very negative connotation in Chinese. Bringing such language into the workplace is even more absurd. Although “For Your Information” isn’t literally “for your own good,” to avoid misunderstanding, it’s best to avoid this phrasing altogether.

So, the basic etiquette of Code Review is: don’t use FYI.

### Why “For Your Own Good” Is Often Wrong

#### Conclusion First

Because no one can truly understand another person.

Do parents truly understand their children? Do married couples really know what their partner is thinking? Do police investigators fully grasp the minds of the suspects they question? Can psychology experts guess why their girlfriend is mad in the morning? And most importantly — do you really understand yourself?

So we can draw a conclusion that has nothing to do with technology: out of courtesy, we should avoid telling others “it’s for your own good.”

#### A Concrete Example

In a recent project, I submitted code that implemented a simple event bus. Both the event's Publish and Subscribe functions used RW locks to ensure thread-safe map access:

```Go
func (eb *EventBus) Publish(event Event) {
	eb.mu.RLock()
	defer eb.mu.RUnlock()
	if ch, exists := eb.channels[event]; exists {
		ch <- event
	}
}
```

The Code Review suggestion I received was to use `sync.Map` instead:

```Go
func (eb *EventBus) Publish(event Event) {
    if ch, ok := eb.channels.Load(event); ok {
        // Note: Type assertion needed here
        ch.(chan Event) <- event
    }
}
```

At most, this was a Nit-level comment. I wasn’t particularly concerned, and it was easy to deal with.

The real issue was that I heard something along the lines of “for your own good” — essentially, “for your own good, you need to understand the difference between RW locks, mutexes, and sync.Map, and then choose the best implementation for the event bus scenario and be able to explain it logically to others.”

From a technical growth perspective, I personally don’t mind this kind of issue. But this kind of thing feels like textbook answers often used in job interviews. As interviews have evolved in recent years, more and more people have grown tired of such rigid, textbook-style questioning.

Of course, people who obsess over these low-level technical differences likely have a deep passion for technology — and that’s not a bad thing. We should respect all genuine effort and seriousness about technology. But tech has many different dimensions.

#### My Interests

Am I someone who has technical passion? Maybe — sometimes. Otherwise, I wouldn’t have quit Fusionist without a backup job, giving up a salary higher than my current one (even including tokens). Whether it’s tech or anything else, all of us want to chase things we find interesting.

From my past experiences, here are some tech topics I care about and enjoy exploring just out of curiosity:

- What’s the fundamental difference between PoW and PoS? Is PoW better than PoS?
  - “[Why PoW Is More Decentralized than PoS](/2024/04/14/Why%20PoW%20is%20More%20Decentralized%20than%20PoS/)”

- How does Ethereum's PoS differ from Cardano’s PoS?
  - Why is Cardano's PoS considered purer? Why is Ethereum’s PoS more decentralized?

- What are the pros and cons of PBFT? How can PBFT be optimized?
  - “[All BFT Consensus Blockchains Are Centralized](/2025/01/05/All%20BFT%20Consensus%20Blockchains%20Are%20Centralized/)”
  - “[Why Ethereum Casper Needs EIP-7251](/2024/06/09/Why%20Ethereum%20Casper%20Needs%20EIP-7251/)”
  - “[PBFT in Blockchain Doesn’t Need a Second Round of Voting](/2024/06/03/PBFT%20in%20Blockchain%20Doesn't%20Require%20a%20Second%20Vote/)”

- How do different types of blockchains handle forks?
  - “[Understanding Blockchain Consensus Mechanisms](/2023/07/01/Understanding%20Blockchain%20Consensus%20Mechanisms/)”
  - “[How PoS Blockchains Handle Forks](/2024/08/22/How%20PoS%20Blockchains%20Handle%20Forks/)”

- What are some interesting potential applications of blockchain?
  - “[Pebbling Game](/2023/05/18/Pebbling-Game/)”
  - “[A Mechanism for Generating Random Numbers on Blockchain](/2023/02/22/A%20Mechanism%20for%20Generating%20Random%20Numbers%20on%20the%20Blockchain/)”
  - “[2/3 Average Guessing dApp Game Design](/2022/12/27/"Designing%20a%20'Guess%202_3%20of%20the%20Average'%20dApp%20Game"/)”

Are these topics useful? Not really. No interviewer ever asked about them, and they’re rarely useful at work. I just explore them for fun. The answers to these questions aren't readily available online, not even ChatGPT can give precise answers. Only after studying, reading papers, and combining with personal experience can one form a technical opinion — whether right or wrong.

So maybe out of mental inertia, I rarely care about overly basic programming questions. Everyone’s different — you can’t force someone to care about something just because of an FYI.

#### FYI: Be a Good Writer

(Yes, I just said don’t use FYI, and here I go again.)

There’s a memorable chapter in the book “[Rework](https://www.joecotellese.com/posts/rework-book-summary/)” titled “Hire great writers.” The point was not about publishing articles or writing reports at work — but that good writers often have a clear and logical way of expressing problems, which is very helpful at work.

Back to tech — today, writing technical articles is quite common. If someone has a strong understanding of lock usage, they could write a solid piece explaining different types of locks and the best use cases. Share it on various platforms, gain thousands of followers, and if the insights keep coming, maybe even publish a book or open source it as a free e-book... FYI... don’t take it too seriously.
