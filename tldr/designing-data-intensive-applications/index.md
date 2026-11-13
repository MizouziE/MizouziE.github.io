---
title: "Designing Data-Intensive Applications"
url: https://www.mizouzie.dev/tldr/designing-data-intensive-applications/
date_read: 2026-01-28
book_author: ["Martin Kleppmann"]
first_published: "2017-03-16"
influence: "4.08/5"
description: "The book that explains what your database is actually doing and why it occasionally lies to you"
tags: ["databases","distributed systems"]
related:
  - title: "Learning Go"
    url: https://www.mizouzie.dev/tldr/learning-go/index.md
---

# Designing Data-Intensive Applications

Kleppmann starts with what a storage engine physically does with a B-tree or an LSM-tree, works through replication and partitioning, and comes out the other side into consensus and why distributed systems are so fond of disagreeing with themselves. The transactions chapter alone justifies the cover price; isolation levels stop being vocabulary you nod along to in meetings and turn into a list of specific things that are going to go wrong. It isn't a tutorial and there's almost no code in it, it's closer to a very well organised argument with a bibliography you could lose a year to. Every chapter makes you slightly less confident about something you've already shipped. That does appear to be the intention.

Without a doubt, it made me much better at my job overnight. I saw things totally different and have not looked back since.
