---
title: "Blade Trick Randomiser"
url: https://www.mizouzie.dev/projects/blade-trick-randomiser/
date: 2026-04-12
link: https://mizouzie.dev/blade
description: "A simple project to create a web app version of the classic roller blading game B.L.A.D.E., which turned into a huge learning opportunity"
tags: ["learning","open source","web dev","claude code","chatGPT","hobbies"]
related:
  - title: "Code Block Click Copy"
    url: https://www.mizouzie.dev/articles/code-block-click-copy/index.md
  - title: "My Top 3 Benefits of Open Source Contribution"
    url: https://www.mizouzie.dev/articles/benefits-of-os/index.md
  - title: "What's the Point of All the Static Analysis"
    url: https://www.mizouzie.dev/articles/whats-the-point-of-all-the-static-analysis/index.md
  - title: "Why Do My Pull Requests All Get Rejected?"
    url: https://www.mizouzie.dev/articles/why-do-my-pull-requests-all-get-rejected/index.md
  - title: "Tum | Modo | Postea"
    url: https://www.mizouzie.dev/articles/then-now-after/index.md
---

# Blade Trick Randomiser

Try it out: [Wheel of Blade](https://mizouzie.dev/blade)

Check out the repo: [MizouziE/blade](https://github.com/MizouziE/blade)

## Original Idea

It all began back in September 2024 when speaking with a friend and long time roller blading enthusiast [Joe](https://www.instagram.com/djrty_joe/) on WhatsApp just after moving away from Marbella and into Cluj-Napoca. We spoke about how we'd miss our blading sessions and I joked about how it'd be cool if we could still play a game of BLADE while living in separate timezones. Joe then reminded me that I'm a web developer...

This was around the same time as ChatGPT starting it's ascendancy, and having worked with it extensively before, I knew the idea was simple enough that I could ask it for help and come up with something basic that could be easily hosted for free on GitHub pages. So I got to prompting and spent an afternoon throwing together a POC and getting it online.

All it consisted of was a single html file to anchor the main functionality which was a _wheel of fortune_ style spinner which lived in a single JS file. The spinner would land on a trick name and the idea was that the user who span the wheel would need to perform the trick. Super simple.

## Years later

The project sat on the shelf, albeit still online, for a year and something while I was focused on my career and settling myself and my family into the new city we'd come to. During which time I'd become part of my new local blading scene and lo and behold ended up having a **very** similar conversation with a new friend and I was hit with Deja vu.

I quickly dug through my github to find the link to the super simple web page and shared it in the local blading WhatsApp group and it was met with an adequate level of novelty. Some smiled and praised the idea, some replied with screenshots demonstrating how awful the UX was. I could only agree with the criticism, it was truly terrible on mobile. On the other hand, the positive feedback told me there was something there worth fixing. The timing of all this was also quite fortunate as I'd only recently really come around to accepting that AI is there as a tool for web and software development and had even signed up for an Anthropic subscription to give Claude Code a proper go. This "fresh" project was a golden opportunity.

## Weekender

Over the course of a couple of weekends in April 2026 I put my project management hat on and started seeing what I could achieve with the help of Claude Code. Having had a few weeks of experience with what it was capable of, and more importantly what it was **not** capable of, I wanted to try and see how well I could manage planning things out. So initially I wanted to just improve things visually, mostly to address that first (correct) criticism received. Beyond that, having quickly exhausted my own ideas for functionality improvements, I realised I should make it suitable for open source contributions. I mean I know for a **fact** that one guy I skate with is a developer, and given the city I'm in, the likelihood of skating with other developers is pretty high, so it only makes sense to have it set up for a community effort.

### Weekend 1 - polish up visually

Two things about me; I don't like JavaScript much and I like frontend design even less. So I just let Claude take the wheel on a redesign and trusted what it came up with. I _did_ take the approach of spending most of my tokens on a proper `plan mode` stage, even asking it to interview me to work out details. What it came up with was nice and simple, clean and not awful to read at all. More importantly, it was a MASSIVE improvement on the rubbish I'd knocked up with ChatGPT the year before

I even went a little overboard with creating a plan and **tracking** that plan [as you can see from this early commit](https://github.com/MizouziE/blade/commit/2454d960c75cc6f6956451a4d3cdb698c0f4de12) where I left the completed bits in to aid with context in later sessions.

I found this way of working good for allowing me to dip in and out of the project and also quite good for making good economy of tokens as it wasn't needing to read the entire project from head to toe on each session initiation. 

After a few sessions like this I was pleased with the way things looked and even the few small improvements in functionality.

### Weekend 2 - robustness, more functionality and ready for contributions

Having made the whole thing look less embarrassing, I sent the link out again and got a little more feedback, which helped shape the direction of the next batch of improvements. During pre-planning of these improvements it dawned on me that this project was very guilty of being fully vibe-coded and there were some huge improvements to be made (on my part) in that respect.

- Robustness:
    - Tests - there were none, at all. Before making any real changes, this gap needed to be filled
    - Structure - if I am going to ask others for help, things needed to be better organised and modular
    - Persistence - one major feature request required a backend... but this is a static site hosted for free on GitHub pages...
- Functionality:
    - Tracking - users want to keep track of their progress
    - Customisation - users want the option of compiling their own lists of tricks
- Contributions:
    - Ready the repo - I had done this before, so just rinse and repeat. See [Energy Price News](../energy-price-news/)
    - This time AI is a factor

## At time of writing

After the huge amount I got done on that second weekend, I thought it was nearly time for a soft launch, so where better to blab about it first than my own blog. I can't think of exactly where this project will go, but I remember being very pleasantly surprised by open source projects I started years ago, so we'll see where it goes.

From a personal perspective, I really enjoyed using Claude Code in the manner that I did. Not only managed to achieve a huge amount in a very short space of time, but also got to experiment with methods of planning and seeing exactly how to get the most out of the AI as a tool. I really look forward to how I will be approaching my future work with these insights.
