---
title: Recent Job Search Record
tags: 
- Job Hunting
date: 2024-09-11 17:00:00
draft_date: 2024-08-01 15:32:19
first_date: 2024-08-01 17:31:18
second_date: 2024-08-08 08:08:08
third_date: 2024-08-21 17:00:00
forth_date: 2024-09-11 17:00:00
---

The Web3 industry is mostly made up of startups and entrepreneurial teams, so there’s little point in documenting so-called interview experiences. Unlike large internet companies, these startups haven't yet developed standardized processes for selecting employees. I also hope my target companies can be more pragmatic. If I can pass an interview by cramming for two days, but fail if I don’t, then what’s the point of the interview? Moreover, passing or failing the interview doesn't really mean anything; it’s not like a standardized exam where scores are compared—it’s just a mismatch of intentions between the two parties. So I don't plan to document specific interview questions, only the companies I interviewed with and my brief impressions.

- [Lumoz](https://lumoz.org/)

They work on a Baas platform and claim to have collaborated with Merlin Chain. I declined the second interview after the first.

On one hand, I dislike Baas platforms—the term itself sounds outdated, and it's a B2B business, which generally isn’t profitable. The work mainly involves deploying some L2 networks, which is pretty boring and has nothing to do with the Computer Layer mentioned on their website.

On the other hand, I think Merlin Chain is a scam, so any platform collaborating with such a project can’t be that great either. Merlin Chain used Ethereum’s code to make a BTC L2. Isn’t that absurd? Moreover, it’s run by Chinese people, and the project is filled with "Chinese wisdom."

- [RSS3](https://rss3.io/)

RSS3 is an OP L2 network currently aiming to improve the TPS of its execution layer client, with two optimization directions: parallel EVM and database optimization.

I remember that L2’s TPS used to be limited by the specific size of each transaction on L1. After blobs were introduced, I'm not sure if the TPS bottleneck for L2 is now at the DA level.

- [Bitget Wallet](https://web3.bitget.com/en/)

Bitget Wallet’s user growth department places a lot of emphasis on Web2 skills, including middleware usage and programming language details.

- [Nubit](https://www.nubit.org/)

A Bitcoin L2 project. The HR interview included some written tests, mainly basic questions like "How does Bitcoin’s 21M cap come about?" Whoever made this test must really love blockchain and "gets" it. I quite like that vibe.

During the technical interview, I learned more about the project. It's not actually a Bitcoin L2 but rather a Bitcoin DA. They built a chain using Cosmos and used Bitcoin’s block height as a data index, marking when DA data is uploaded to the chain.

If other chains use this chain as a DA layer to upload data, an index is needed to find their own DA data, and Bitcoin’s block height serves as the index. So this project is merely riding the Bitcoin wave and doesn’t really have much to do with Bitcoin.

- [Elastos](https://baike.baidu.com/item/%E4%BA%A6%E6%9D%A5%E4%BA%91/23681499)

If you can call it an interview. It was very hasty, just a 10-minute phone chat. The task was for a 3-month project related to Bitcoin, setting up a Bitcoin node, and developing some middleware. The project timeline was 1 month for research, 1 month for development, and 1 month for delivery. After the project ends, the team disbands. Unfortunately, I forgot to ask what the specific project entailed.

I want to mention my feeling about projects with such defined timeframes. There are two scenarios: either the initiator is very capable, with extensive project management and development experience, and the project itself isn’t very technical or challenging, allowing them to control the timeline precisely. The second scenario is that the initiator wants to make quick money but lacks both technical skills and funds, and is ignorant of the project’s difficulty or how to form a long-term team. Given that the project timeline includes research, it’s clear this company falls into the latter category.

- [HashKey Group](https://group.hashkey.com/en)

This was one of the better interview experiences. The interviewer seemed quite experienced, asking questions as they came to mind, at least keeping the conversation on the same wavelength, rather than coming off as rigid.

They were hiring for an exchange wallet, so the candidate needed to understand various blockchains, especially the ins and outs of sending and receiving transactions. Concerns about forks, rollbacks, and backend development (like middleware and database issues) were also raised.

From both [last year’s](/2023/06/29/最近找工作的记录（6月份）/) and this year’s interview experiences, I’ve concluded that for exchange wallet departments, the "ideal candidate" is someone who is a "Web2 backend developer with blockchain knowledge." This means someone with Web2 backend experience who also understands basic blockchain concepts and knows how to handle transactions.

Web2 backend development typically involves familiarity with middleware and databases, like Kafka and Redis. Interview questions might include designing and implementing a message queue or solving distributed transaction problems in MySQL—classic Web2 development issues.

Of course, I know almost nothing about Web2, so I always struggle when asked about it in interviews. Later, I just started admitting I didn’t know. If I hadn’t been in the blockchain industry, I might have delved deeper into those technologies. I remember right after graduation, I was even translating and learning some [classic technical topics](https://github.com/smallyunet/advanced-java).

Another good point about this interview was that the interviewer didn’t ask, “What is Go's GMP scheduling mechanism?” I’ve been asked this many, many times and still have no interest in looking up the answer. You can search "Golang GMP" on Google or YouTube and see how many of the results are in Chinese. Who’s actually interested in this question?

- [Matchain](https://www.matchain.io/)

The HR was nice and cute, saying the project is a Cosmos-based chain with a testnet and many users, preparing to launch the mainnet.

The interviewer was pretty bad, constantly asking about L2 details. I said I didn’t know, yet they kept asking until I was speechless. They didn’t ask about anything else. I later learned they are using an opBNB L2, so it’s a BNB ecosystem project.

- [TokenInsight](https://www.zhipin.com/gongsi/9f4beb0fb87ed3bf1nJz2t60Fw~~.html)

The interview experience was decent (regardless of the result or if we were a good match). The interviewer asked questions impromptu, similar to the interviewer from HashKey Group, speaking slowly, thinking as they asked.

This company is working on a custodial wallet. They mainly asked basic blockchain concepts.

They gave a written test with a "simple" Leetcode question, asking to choose between merging sorted linked lists and reversing a linked list. I haven’t paid much attention to such topics lately or practiced coding problems, so I probably wrote the wrong code during the interview.

Interestingly, I wrote a detailed analysis of these two problems [years ago](https://gub.smallyu.net/%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84%E4%B8%93%E9%A2%98/Linked%20list/%E7%BF%BB%E8%BD%AC%E9%93%BE%E8%A1%A8.html).

That analysis is part of a chapter in "[Ground-Up Blockchain](https://gub.smallyu.net/%E5%B0%81%E9%9D%A2.html)," a book I started writing, the source code for which is here: [smallyunet/ground-up-blockchain](https://github.com/smallyunet/ground-up-blockchain). The project has been abandoned for a long time.

- [Gate.io](https://www.gate.io/) (Spot Trading Department)

A casual chat. I expected the "Exchange + Golang" role to involve a lot of Go technical details, but they only asked general, high-level questions like "What practical uses does blockchain have in real life?" I suspect the interviewer had already made up their mind to pass on me before the interview even started.

- [Gate.io](https://www.gate.io/) (Gate Chain Department)

From the HR’s perspective, the project is a Layer 2 chain that mimics opBNB with some optimizations, and they’re also mimicking TIA in the DA area.

The technical interview was quite interesting, but I’m too lazy to go into detail :P

One interesting thing was that before the interview, the HR repeatedly advised me to prepare, review some stuff over the next couple of days. This sudden "kindness" made me a bit suspicious. I wasn’t sure what the motive was—perhaps the HR had a commission incentive?

I’ve never understood what to prepare for before an interview. Your actual work is always on your mind, and cramming for non-work-related tasks over two days won't yield results. In short, what you know, you know; what you don’t know, you can’t learn in two or three days. So what exactly is there to prepare for?

As for employers, they should be looking at a candidate’s work experience over the past few years, not just whether they’ve crammed for two or three days.

### Phase Summary

I haven’t found a job yet, and the job search isn’t over, but since this has been going on for quite some time, I think it’s necessary to wrap up this blog post for now and start a new one later. As for documenting interview experiences, it seems I’ve always recorded every [job search](/tags/%E6%89%BE%E5%B7%A5%E4%BD%9C/) since I started working, and I’ll probably continue to do so.

This phase of the record roughly spans from July 20 to September 15.

Around July 20, I started thinking about quitting and began my first interview. September 15 is the day I officially resigned. I haven’t been sending out many resumes for the past two weeks because I haven’t fully left my current job and haven’t wanted to start a full-on job search yet.

How’s the job market this year? Well, it’s definitely not better than last year. It feels like there are even fewer job openings this year. How should one feel about this? Probably a mix of no regrets about the past, slight anxiety about the future’s uncertainty, and a blind sense of optimism about what’s ahead.

By the way, the entire cryptocurrency industry is valued at around $2 trillion. Can we find a $3000 job to sustain ourselves in this industry? Will the world give us that chance?