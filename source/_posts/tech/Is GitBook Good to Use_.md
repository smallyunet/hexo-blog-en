---
title: Is GitBook Good to Use?
tags: Tools
date: 2021-11-21 14:21:20
draft_date: 2021-11-20 22:51:01
---

On October 15, I wrote the following:

> Using Cloudflare's DNS, I changed the subdomain `gub` from a CNAME pointing to GitBook to point to GitHub. Over 72 hours have passed, and with DNS proxy enabled, it still redirects to GitBook. It appears to be a 302 forward.
>
> It's not a caching issue. I've opened dev mode and purged everything multiple times. I suspect Cloudflare's proxy service for forward records updates very slowly or has a bug. The elapsed time has definitely exceeded the TTL.

A month later, the issue was inadvertently resolved. I'd like to elaborate on the problem I encountered.

### Background

This involves two concepts: DNS (Domain Name System) and CDN (Content Delivery Network). If you're not familiar with web services, you might want to understand their connection and differences first.

### The Problem

Initially, I planned to write an open-source book and chose GitBook as the platform. GitBook is well-known, and the open-source rendering tool GitbookIO/gitbook makes it the best choice for open-source books. After a brief trial, I didn't notice any issues with the platform. I created a Workspace, then a new repository on GitHub, linked it to GitBook, and everything went smoothly. I tested GitBook and GitHub's auto-sync capability, and while there could be minor conflicts, they were easy to resolve.

I bound a custom domain to GitBook. The domain `smallyu.net` is hosted on Cloudflare, and the subdomain `gub.smallyu.net` has its DNS records set on Cloudflare. It's well-known that Cloudflare offers free CDN services, which can be enabled by toggling the orange button in DNS records:

![CDN Toggle](1.png)

The toggle was on when pointing to GitBook. A few days later, GitBook underwent a major upgrade, completely changing the writing interface. A day or two after the upgrade, I wanted to update some pages but found the new GitBook workflow counterintuitive and buggy. Each modification was like a Git Pull Request, and clicking the edit button each time created a new PR entry. With multiple PR records, the page state became uncontrollable, with no option to delete PRs, which was frustrating. Additionally, refreshing the edit page multiple times due to slow internet speed resulted in several PR entries, making it hard to identify recent changes. I even complained:

> The online editing on GitBook is too difficult to use. You can't delete commits, create new documents, and the cursor jumps around... How are they being responsible to users?

I decided to abandon GitBook and switched to docsify, deploying the pages on GitHub Pages, and changing the `sub.smallyu.net` domain to point to GitHub. After updating the DNS records, the change didn't take effect (Cloudflare's CDN Proxy was on). Visiting https://gub.smallyu.net still redirected to the old GitBook page.

I initially suspected a DNS TTL issue because in Proxied mode, the TTL value is set to Auto. Given Cloudflare's numerous CDN nodes and my domain's low traffic, DNS record updates might be slow. After waiting three long days, the issue persisted, with the domain still redirecting to the old page.

`dig` results showed:


```
gub.smallyu.net.	300	IN	A	104.21.81.212
gub.smallyu.net.	300	IN	A	172.67.146.253
```


No CNAME records were found. This indicated Cloudflare's CDN IP. The domain resolved to the CDN, but the CDN didn't return the expected new content.

Interestingly, disabling the CDN Proxy resolved the issue, with the domain correctly resolving to GitHub's A and CNAME records, showing the new page.

What was the problem? Cloudflare's CDN hadn't refreshed the content.

Cloudflare offers Caching configurations and Purge capabilities:

![Caching Configuration](2.png)

Despite multiple "Purge Everything" attempts, the CDN content didn't refresh. Developer mode was also ineffective:

![Developer Mode](3.png)

Even setting custom page rules to bypass caching was futile:

![Page Rules](4.png)

I even deleted the original Workspace and deactivated the account on GitBook to no avail.

Ultimately, as long as Proxy was off, domain resolution worked. I thought it might be a CDN bug or GitBook using a 302 forward that the CDN couldn't refresh correctly.

### The Cause

Recently, while visiting https://gub.smallyu.net, the page lagged, reminding me it wasn't using CDN. Googling the issue led me to useful information. Previously, searches only yielded cache update tips; this time, I found different insights.

Cloudflare has partners with control over Cloudflare DNS. When the domain was pointed to GitBook, the CDN's DNS fell under GitBook's control, overriding Cloudflare settings.

Relevant links:

- [DNS subdomain no longer works nor redirects to anything](https://community.cloudflare.com/t/dns-subdomain-no-longer-works-nor-redirects-to-anything/240984/7)
- [Subdomain CNAME does not update](https://community.cloudflare.com/t/subdomain-cname-does-not-update/280696/2)

I emailed GitBook Support:

![Email to GitBook Support](11.png)

Surprisingly, GitBook Support resolved the issue within a day:

![GitBook Support Response](12.png)

Tests confirmed everything was normal, validating the cause.

### Lesson

Who would have thought that a widely used service like Cloudflare would delegate domain resolution control on CDN to partners?

Who would have thought GitBook's product wouldn't delete domain records even after the user deleted the Workspace and deactivated the account?

The lesson here is to shift from a user mindset to a developer mindset. Influenced by the concepts of "official" and "authoritative," we often doubt our methods and errors first when using platforms, rarely questioning the platform itself. Even if it's clearly a platform issue, we don't prioritize contacting platform support. This point can be expanded into many topics for future discussion.

### Tools

I now use the Rust team's `mdbook`. GitBook feels outdated and not well-maintained. docsify, though suitable for project documentation, lacks the stylistic focus on text and inter-page links like a book. While mdbook's style isn't trendy, it's functional, fast, and practical.
