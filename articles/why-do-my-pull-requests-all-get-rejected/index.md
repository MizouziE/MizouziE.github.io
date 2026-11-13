---
title: "Why Do My Pull Requests All Get Rejected?"
url: https://www.mizouzie.dev/articles/why-do-my-pull-requests-all-get-rejected/
date: 2026-08-11
description: "I have made many PRs as open source contributions, and only now realise why so many were closed"
tags: ["web dev","PHP","laravel","open source"]
related:
  - title: "Laravel Live London 2026 Takeaways"
    url: https://www.mizouzie.dev/articles/laravel-live-london-2026-takeaways/index.md
  - title: "How to test protected functions with PHPUnit in your Laravel app"
    url: https://www.mizouzie.dev/articles/how-to-test-protected-functions-with-phpunit/index.md
  - title: "Undefined type 'Spatie\\Permission\\Models\\' . 'Permission' | 'Role' | 'HasRoles'"
    url: https://www.mizouzie.dev/articles/undefined-type-spatie-permission/index.md
  - title: "What's the Point of All the Static Analysis"
    url: https://www.mizouzie.dev/articles/whats-the-point-of-all-the-static-analysis/index.md
  - title: "Code Block Click Copy"
    url: https://www.mizouzie.dev/articles/code-block-click-copy/index.md
---

# Why Do My Pull Requests All Get Rejected?

![they Always Be Closing (my PRs)](https://www.mizouzie.dev/images/always-be-closing.jpeg)


## Is it something personal?

I recently looked to see how many Laravel PRs I've had closed vs how many were merged and was not all that shocked to see that I am 7-0 in that respect. It hits even harder because I work with a guy who is currently sitting in 15th place for most contributions to the framework, which is **incredible** work from him! I never thought I'd be anywhere near that level, but apparently the merge rate overall is around 55%, so I'm definitely right down at the bottom of the barrel. But why?

Did I do something to offend someone in their offices? Did I insult their grandmother or something?

I've only ever tried to add things to the framework which _I_ felt were missing and assumed there must be others out there that needed the thing I proposed. But each and every attempt was met with the infamous:

>"Unfortunately, I'm going to delay merging this code for now..."

Surely *every* idea I have can't be garbage. Can it?

## Look at the hard facts

I have a habit of getting too caught up in my own head. I often pursue curiosities that are so far removed from my task that I can forget why I even started. It's something I appreciate, because it helps me go that extra mile **but** I have been making a real effort to stay on top of it so I don't waste my energy on things that aren't needed in that precise moment. The thing was, I was only applying this reflective mindset to work. I needed to apply it more to my personal projects and the open source contributions I was trying to make.

So where do I start reflecting?

I started by revisiting the PRs I had submitted. All of them.

### Are my PR descriptions too thin?

Looking through them all, surely it's a resounding NO. One thing I'm often guilty of is over-explaining things. Dev calls with my team go on for over an hour when I'm leading them because I'll go into way too much detail, mostly to make sure I actually understand what it is I'm talking about! But the result is that I've covered pretty much everything most of the time.

I also second guess myself a lot, and I'll mention the questions and concerns I aimed at myself and give some explanation of why it _was not_ a problem.

### Is my test coverage insufficient?

Heavens no! PHPUnit testing framework author **Sebastian Bergmann** is THE most iconic name in the PHP world, hands down. Literally the very first name I knew of when I started in all this because I learnt the TDD way first and foremost. I'm a very firm believer in well tested code. So all my changes definitely had coverage and I even used the changes made to existing tests to illustrate how my change worked.

On the flip-side to my terrible track record in Laravel, I did once get something merged into `nesbot/carbon` and it was thanks to showing the changes required to the tests that the maintainers immediately gave my direction the green light. Probably the contribution I'm most proud of.

### Do I waffle too much?

As already mentioned... _maybe_. But it's only a maybe. I always tried to frame things well and give before and afters as well as a fair reason _why_ I believed the change was worthwhile. I mean, an over-explanation should not be a reason to reject a change proposed to some code.

### What kind of changes are being proposed?

This is the key, right here. What is the general type of change that I am trying to get included in an already large and widely used framework?

I have tended to try to add entirely new features or convenience additions where I've tried to combine existing things because I was tired of repeating the same combinations. Now think about that kind of suggestion when you are maintaining an already quite huge framework. Do you really want to expand the surface of an existing API? Has there been any visible demand for this addition? Or is it just some guy punting an idea across the interwebs?

That's it.

## A little breakdown

- [#60937/#59609](https://github.com/laravel/framework/pull/60937)
    - `whereDoesntBelongTo()`: This was emulating what I thought was proper symmetry like how other eloquent methods have their counterparts. I'd never seen any public discussion of a need for these, but I had wanted to use them once or twice myself. I had not considered that a developer can already write `whereNotIn(...)`, so it's added surface for a very small gain.
    - I had also committed the cardinal sin of just resubmitting the same code twice out of spite :see_no_evil: and that is **never** a well received action.
- [#58342](https://github.com/laravel/framework/pull/58342)
    - `forEachCrossJoinSequence()`: Same thing here where I'm just smooshing two existing methods together to save myself typing 6 extra characters. To be fair, in this one I actually got a reply saying that the problem I'm solving isn't really all that bad.
- [#60604](https://github.com/laravel/framework/pull/60604)
    - `Arr::exceptValues()`: Do we see the pattern emerging? It's the same thing. Small convenience, tiny gain.
- [#61088](https://github.com/laravel/framework/pull/61088)
    - `SoftDeletes` + `PivotModel`: This one was me scratching an itch where the repo I work on for my day-job has a few unconventional ways of doing things that (as it turns out) go completely against Laravel doctrine. I discovered later that the docs literally say "do not do the thing here as we won't support it", but here I was, trying to _fix_ a problem they outright said they were not interested in.
- [#53527](https://github.com/laravel/framework/pull/53527)
    - `$this->artisan(...)->expectsTable(...)` default style param: This one I did slack on explanation, but it was also short sighted on my part. If I'd have thought more from a maintainer's point of view, I may have at least started a discussion about the issue I thought I was fixing.
- [#53843](https://github.com/laravel/framework/pull/53843)
    - This one was a real bug fix and the only one that almost made it. I had unfortunately not covered one thing and someone else got in there first but kindly mentioned on my PR what I'd missed and linked to their correct fix which nicely did reference the issue I had opened about the bug. Pity it was only downhill from there.

So the issue is mostly that I'm pitching cold feature ideas into a reputable repo that has a history of rejecting them. And on real reflection, that close guarding of the repo's maintainability is a **huge** factor in the success story of Laravel. It's been around for yonks and looks set to continue. In order to be a part of it, I need to get on the same wavelength.

## So where do I go from here?

Onwards. Take the lessons learned. Keep up the pausing and reflecting rather than just keep doing the same things and expecting different results. Keep contributing to a range of different projects, as each one will teach me something different. Most importantly keep my ego in check and don't take it personally, it quite literally isn't.
