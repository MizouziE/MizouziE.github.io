---
title: "-r(ead)w(rite)x(ecute): Documentation"
url: https://www.mizouzie.dev/articles/rwx/
date: 2022-08-09
description: "I wrote a big piece of documentation recently. Here's how it drastically improved my developer flow."
tags: ["web dev","documentation","learning"]
related:
  - title: "Code Block Click Copy"
    url: https://www.mizouzie.dev/articles/code-block-click-copy/index.md
  - title: "Tum | Modo | Postea"
    url: https://www.mizouzie.dev/articles/then-now-after/index.md
  - title: "Send SMTP Mail from Hetzner Server with Firewall"
    url: https://www.mizouzie.dev/articles/send-smtp-mail-from-hetzner-server/index.md
  - title: "How to test protected functions with PHPUnit in your Laravel app"
    url: https://www.mizouzie.dev/articles/how-to-test-protected-functions-with-phpunit/index.md
  - title: "Space is the Place: Reasons to explore Twitter Spaces"
    url: https://www.mizouzie.dev/articles/space-is-the-place/index.md
---

# -r(ead)w(rite)x(ecute): Documentation

![working on a laptop](https://www.mizouzie.dev/images/rwx.jpeg)


I've hit a year now working as part of a development team with PHP and Laravel. Before I was ever involved, the project I've worked most on was already running and comprised of a RESTful API, a CMS and umpteen instances of frontend applications that showed all the wonderfully managed data to the end users.

Even though I've probably spent a few hours a day working here and there with the various code bases, I've only very recently felt like I *know* the ins and outs of how it works and that is thanks to having been given the task of writing the documentation for the whole thing. The project existed and was managed by a very small group of people, so up until now, there was no real need for docs as the group were the ones requesting features as and when they needed. However, there will soon be a new team introduced to the system and it became apparent that the application was far more complex than first envisaged.

So, being the junior in the company, the template was chosen and the repo was handed over to me to fill in. At first I thought it'd be a nice easy task as most of the application was fairly self explanatory. I was mistaken. There were corners of this thing I didn't have the first clue about... so how was I gonna write about how it is meant to be used?! I had to ask or just figure it out.

A week had gone by and I'd only managed to map everything out by headings plus some `lorem ipsum` filler and it was amounting to around about 40 pages... I couldn't believe it was possible to have worked so many hours on something and still have such a shallow understanding of it. I needed to get writing. So I started out with what I already understood.

Once I'd exhausted my comfort zones I had to research. I clicked through every single possible link and button and tried every preference and setting. I spent almost another week exploring this tool I'd been staring at the bones of for the past year. And I'll tell you what, I was kinda proud of how good the tool was! It was powerful, fast and more sensibly laid out than most web apps I've had to use in the past. I was amazed I'd had no idea how good the thing I'd contributed to turned out so good.

The shocks did not stop there though.

I completed the task, as far as writing and first draft and formatting goes, a week ago and have had a few days on other projects and traveling since. This morning I was told to investigate and fix a bug that, a few weeks ago, would have had me scratching my head for a good few days. No head scratching today though. Before I new it, I'd checked out the reported bug, checked the raw API response that lead to it, browsed through the routes for the API and identified the controller and method for that particular endpoint and was running through the query builder string in my head to see just why the problem was occurring.

I saw no obvious problem... But before that had a chance to stump me, one particular chapter which I'd had to thoroughly investigate popped into my head and I went straight to that part of the CMS to see if what I predicted was present. IT WAS!

Just from the new found deep understanding after writing the documentation, I was able to imagine exactly what might be causing the issue. It was a first for me with this project. My debugging had never flowed so smooth. I took this understanding, went back to the controller and added in what was missing to prevent the bug from existing. Done.

It took me just over 50 minutes total. Never dealt with anything on this project in such a short timeframe.

Before, I was just trying to -r-x (read the code and execute a task) and it was a painful and frustrating learning curve.

Now that I am -rwx (read the code, write about the code and **then** execute a task) I feel like I am a level or three higher in my developer-acumen. It feels good.

One more reason that writing the docs is incredibly important. It isn't just the app users that will benefit. The junior devs do too!
