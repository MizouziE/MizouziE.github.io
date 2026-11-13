---
title: "What's the Point of All the Static Analysis"
url: https://www.mizouzie.dev/articles/whats-the-point-of-all-the-static-analysis/
date: 2026-11-06
description: "Often static analysis tools are lauded for their preventative powers, but they seem to have great value in retrospect too"
tags: ["web dev","artificial intelligence","opinion","open source","code quality"]
related:
  - title: "Why Do My Pull Requests All Get Rejected?"
    url: https://www.mizouzie.dev/articles/why-do-my-pull-requests-all-get-rejected/index.md
  - title: "A Reply from the Machine"
    url: https://www.mizouzie.dev/articles/a-reply-from-the-machine/index.md
  - title: "Code Block Click Copy"
    url: https://www.mizouzie.dev/articles/code-block-click-copy/index.md
  - title: "New Side Project - AI as a Creator"
    url: https://www.mizouzie.dev/articles/new-side-project-ai-as-a-creator/index.md
  - title: "How to make your GitHub project pop on social media"
    url: https://www.mizouzie.dev/articles/make-your-github-project-stand-out/index.md
---

# What's the Point of All the Static Analysis

![Static Analysis Comic Book Hero](https://www.mizouzie.dev/images/static-analysis-hero.png)


For those of us that work in the field of software and web development, and all of these types of things, it's inevitable that we have to work with AI to keep up with the way things are going. It's been described by those that really know what they're talking about as a tool, and it's important to think of it that way.

In order for a tool to be useful, usually you have to use it alongside other tools. There'll be more than one tool in a carpenter's toolbox, for example. A carpenter can use a saw, that's great, but how did he know where to cut? How did he know how far into the wood to cut? Where is he marking the line where to cut? So the carpenter with a saw is less useful than a carpenter with a saw and a notepad and a pencil and a set square and a measuring tape.

That's the way I like to think about static analysis. Static analysis is the kind of tool that makes other tools like AI way, way more useful.

## What the conference talks said static analysis is good for

When I first started hearing a lot of the people singing praises of the static analysis tools like PHPStan, they were coming at it from an angle of;
> - we're going into an AI future...
> - all of the development is going to be AI generated code...
> - so we need to have these guardrails in place...

And they're correct. I agree with that. That's absolutely right. That by itself makes it a worthwhile endeavour. That was probably why I got started with it as well, because I heard it enough times from enough respectable people.

I only today really saw the true value, or an alternative huge value, which it has. So that's what I want to speak about now.

## PHPStan on a long-lived codebase

A good working example of how this tool can be used, apart from just being a guardrail for AI, is to help codebases which have lived for a long time. Not that there are many, because most don't see more than 6 months of usable life, but for those rare cases where a repo has existed for 5 years, 10 years, 15 years, more, who knows?

PHPStan is one tool that I am trying to use currently on a repo that has existed for quite a while. One fantastic way that it can be used is to help you identify where terrible decisions have been made along the way.

Obviously, things are going to change over time. This is just how things work. Back in the day, 2005, 2010, whenever it was, people maybe didn't have tools like this available to them, and PHP itself was a bit more loose, a bit more dynamic. It wasn't quite as bad as JavaScript, but it's still considered a dynamic language. So you could get away with being creative. You could exercise sort of a poetic licence when writing it.

Fast forward to 2026, and tools like PHPStan are well established, well developed, quite strong. Now, in running PHPStan on only level 2 (I mean, it goes up to 10) over the codebase that I work on every day, it came up with thousands of errors. Thousands upon thousands.

So I've put it on myself to go through them, level by level, eliminating errors at their source as much as I possibly can. And although I've only made it to level 2 so far... it has shown me some things.

## Traits and interfaces under strain

I think the biggest impact thing it's shown over the time I've spent correcting the errors that came up while trying to clear the baseline, has been that people have a tendency to be a little bit too lenient when using interfaces and traits.

Traits and interfaces have their use cases, they have their places, they often work well when combined. But there are times when systems can be designed one way, and over time the business requirements drift. And because they were written originally with traits and abstract classes and things like this, those drifting business requirements can pull the things that were once related further and further apart, yet we leave them linked by these shared things, and it just forces too much. And eventually those things no longer suit what they were originally designed for.

## How I actually got there

If I'm being totally honest, I didn't come to that conclusion immediately.

The thing that helped me see this, like what I'm trying to describe now, is that I installed PHPStan on our project maybe a month ago, spent some time going through it to get it clearing on level 1, get things to be okay, made the minimum changes required, and we had a completely empty baseline, which was great.

I set up some automation and I asked it to just run PHPStan once a month and then report back what was on the baseline, because I expected people to, as they were developing things, to just add little bits and bobs to baselines and they'd need tidying up. But that never really happened. What happened was it just stayed as an empty baseline on level 1. And because each of us do use AI, or because we have our CI workflows that will run PHPStan and fail if anyone's introduced something ugly, we've been forced to fix it there and then, which is actually good! It's just that the baseline stayed flat.

So I thought after a month, let me ramp up the level and see how it goes. So level 2, generate the baseline... There were things... There were big chunks of things.

And there was a conversation that came up while I was fixing just one of those things, a fairly simple thing. Where I needed to add an annotation for a parameter that was accepted by a method in an enum. Now this enum method accepted, I think it was 12 different class types. And the annotation looked horrendous, all listed one after the other, one after the other. And it was suggested, why can't we just use the interface that all of these shared? And I thought, yeah, that's a good point. Why didn't I do that? That's kind of stupid.

So I went back, I put the interface on it instead, and 11 of them passed, and one of them still came up as an error. And I was like, well, why? All of these things, they all have a similar name. All of the classes had the prefix of the model they came from, and then the type of subclass suffix was the same. Why is this last one not matching up? Surely they all used the interface.

And that forced me to go and check. Of the 12, this one, I didn't think to go and check them all individually, but this one, **now highlighted**, I went and checked, and it did not use the interface.

That is precisely the pattern which using tools like static analysis will help you identify and eventually fix. See, we never would have known that we needed to fix this if I hadn't gone through this and had this conversation.

So it's not automatic. It's not that it just fixes everything. What it does is it forces you to do things properly. And because it's forced you to do things properly, it makes things visible.

And that thing is not necessarily always a bug, but it can be a code smell. It is something we've had running for I don't know how many years, but the _drift_ happened over years. And because nobody thinks in those terms (it's impossible to, we're only human), nobody noticed. And it was thanks to using static analysis and a conversation in a code review that led to me realising, oh my God, this is actually wrong. And the use of interface that we have here is incorrect.

## Accept the ugly version

Now, the argument was that we should just use the interface, and normally I'd agree. That's kind of like a rule of thumb we have, to prefer using interfaces, but we're writing them from the top down. You write the interface in order to be used instead of this long list of stuff. And it does look nicer, it's more aesthetically pleasing, it's easier to think about, and it groups things like models quite nicely in your head, which at the end of the day is good design.

So I understand why it was suggested. I even agree with it. I even think it was nice, that's why I went ahead and tried it. However, it shouldn't be used when it's not the correct thing to do. And it's sometimes very hard to see that it's not the correct thing to do, or that it's become the incorrect thing to do. I think it's important to make that distinction because, like I say, this can happen over time.

So I think the important thing to remember, like the point of what I'm trying to say here is, when this happens, it's important to have this conversation. It's important to try out the thing that's being suggested. And it's also important to take the lesson that it was incorrect, and then to accept the ugly version that is against everything aesthetically pleasing. It turns your stomach to read. It's horrible. It stands out. But it's visible.

And because it's visible, you're able to come back and fix it, or at least see things from a perspective you didn't know you needed to see things from, which will, in the end, allow you to come back and make that refactor and make things make more sense.

And if it is refactored and rewritten, more to the shape in which it is being used today, then at the end of it all, you'll end up with more robust and dependable systems. And that's pretty much what we're aiming for. That's the aim of the game.

And if they do make more sense, they're more simple, easier to grok, then, well, tools like AI are going to be able to understand things better and be much less likely to make mistakes. So it means we will be able to achieve the dream of;

> Claude, go do this for me. I'm going to make a cup of tea. Ping me when you finish, mate. Thanks.
