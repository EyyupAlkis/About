---
layout: post
title: Road to Open Source
categories:
  - "Open Source"
  - "Web Development"
lang: en
---

Open-Source-Software is pervasive throughout our industry.

While all-proprietary systems may have been a thing twenty years ago, modern tech stacks are comprised of tons of OSS.
And although OSS lives and breathes through collaboration and contribution of all community members, most of us remain nothing but users.

In this article, I’ll show you why I think that’s a bad thing and I’ll give you a guide on how to make good contributions yourself.

<!--more-->

## Why should I care about OSS in the first place?

Contributing to OSS is a good way to deepen your knowledge in a field.  
Diving into the internals of software you’re using all day will help you develop a stronger understanding, but also broaden your awareness for other aspects of it.
You’ll get to know a lot of interesting people, often from all over the world.
Don’t underestimate the value of contacts across the industry!

Open Source will also introduce you to a very special way of working: It may be considered the pinnacle of distributed and asynchronous work, makes heavy use of process automation and tends to set very high quality standards.

Also, it just looks good on your resume.

*From a company’s perspective*, OSS is a highly collaborative way to make shared efforts across the industry.
Instead of people from your team or maybe people across your company collaborating on shared tooling and knowledge, OSS brings that discourse up to the industry level.
It’s similar to the highly decentralised public discourse of democracies: Spread out across blog posts, books, meetups, conference talks and - maybe most importantly - software repositories.
OSS fosters knowledge exchange and results in industry standards like Wordpress, Kubernetes, Git or Linux.  
Making good use of it will increase your company's productivity; contributing back will improve your engineers’ skills and give you a good standing in the developer community.
After all, there’s a reason that even Microsoft is jumping on the OSS bandwagon.

> Did you know the term “Open-Source” was coined in 1998 by Christine Peterson?  
> She was part of a committee looking for a label to describe Netscape’s open development process.
> In contrast to the politically-influenced “Free Software”-movement, Open Source emphasises the pragmatic business advantages of participatory community efforts.
> ([Source](https://opensource.org/history)) 

## You got me. How can I contribute?

Awesome! Before you contribute, you’ll need to decide which project you want to contribute to.

If you ask me, it doesn’t really matter if you start with small or large ones - just pick the ones you regularly use yourself, you’ll likely be more familiar with them.
Before doing anything, you should read the contributors guide.
It can often be found in a file called `CONTRIBUTING.md` and will contain everything you need to know about the project.
There will be info on the code of conduct, ticketing system, how to get the code working locally, maybe a codebase introduction and much more.
If there’s a link to a Slack or something similar, make sure to join and introduce yourself to the other contributors! 

Next, you need to decide on how you want to contribute. It’s not limited to code at all!  
You can help with documentation, create feature requests and bug tickets, donate, help with maintenance and moderation, do code reviews, fix typos, spread the word by writing or speaking about the project or anything else you can imagine.
Each way of contributing works a little differently, but I’ve collected three common things that you should always keep in mind:

### Reach out before starting to work.

Often, there’s already someone working on the same problem.  
Or it’s not precisely aligned with the project goals and needs a shift.  
Or there’s that brand new feature you could make use of in your implementation!

Talk to others before diving in head-first, it will save effort and result in better work.
For small things, reaching out via Slack is alright - but if it gets somewhat bigger or may lead to a discussion, go ahead and create a new issue on GitHub.
It will make it a lot easier to reference in the future and is also easier to maintain than Slack. 

### Be precise and provide sufficient information.

OSS work is inherently asynchronous.  
Spend time on crafting precise and easy-to-understand posts.  
Make an effort when writing your feature requests or bug tickets - whoever reads them will have a very hard time deciphering them unless they’re stated clearly!  
If there’s a predefined issue template, use it.  
Give the others all the information that is required to fully understand the issue.  

For bug tickets, think about providing a minimal reproduction case.
Instead of pasting your code, try to recreate the problem with a project as simple as possible!
For WebDev related stuff, a link to a CodeSandbox containing the reproduction case works great.
For other projects, just send a link to a repository.

When creating a feature request, you should place a significant emphasis on the problem you’re trying to solve instead of the feature you need for that.
Oftentimes, people have problem X and think of solution Y, so they make a feature request for solution Y.
But maybe there’s a much simpler solution to the underlying problem that they didn’t think of.
In order to find that optimal solution, it is important to emphasise X rather than Y. 

### Learn to make good use of Git. 
Git is not really beginner-friendly, I know.
But it’s an incredibly helpful tool once learned, so you shouldn’t neglect it.
If you’re totally new to Git, I can recommend [“How to Contribute to an Open Source Project on GitHub”](https://egghead.io/courses/how-to-contribute-to-an-open-source-project-on-github) by Kent C. Dodds.
Here are some rules of thumb I use to make my code easier to review: 

1. Craft good commit messages.  
   Commit messages are like abstracts of their contained changes, making it easy to grasp the high level of your Pull Request (PR).
   If there’s in-depth information you’d like to share - maybe your reasoning for making some design change - put them into the commit body!
   It gives you enough space to elaborate freely, but doesn’t clutter the commit log.

2. Create small commits with a single purpose.  
   It will allow code reviewers to go through your PR change by change, making it a lot easier to understand.

3. Tidy up the commit history to make it easily understandable.  
   Using interactive rebase, you can modify order and messages of commits and even squash some of them together.
   It allows you to give your PR a more understandable structure. 

If you’re like me, Pull Requests will intimidate you at first.
It may seem as if maintainers and code owners scrutinise every little aspect of your code.
They’ll ask you to add tests or abide by the project’s style guide, and that’s totally fine - it just helps maintenance in the long run.
Also, you’ll get used to it after a bit.
I promise.

## Now go and contribute!

Working on OSS can be an incredibly fulfilling experience.  
Over the past couple of months, I’ve contributed to high-level projects like [Blitz.js](blitzjs.com), [Next.js](nextjs.org) or [Babel](babeljs.io).
I’ve seen my code run on the stage React Europe 2020 (a very influential conference in the React space) and got to know a lot of incredible people.
I’d like to encourage you to try it out yourselves - so feel free to share your contributions, I’d love to see them!
And if you still don’t know where to start - hit me up ([@skn0tt](https://twitter.com/skn0tt)), I’m sure we’re gonna find a project for you.

> This article was originally published in [HPImgzn #26](https://hpimgzn.de/alle-ausgaben/hpimgzn-26/)
