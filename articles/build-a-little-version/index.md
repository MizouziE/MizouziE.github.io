---
title: "If it doesn't make sense, build a little version"
url: https://www.mizouzie.dev/articles/build-a-little-version/
date: 2022-05-15
description: "Been a while since I wrote a longer article, so here's a longer article explaining what it is I have been doing. Basically levelling up a bit. Learning to build and building to learn!"
tags: ["web dev","laravel","api","learning"]
related:
  - title: "How to test protected functions with PHPUnit in your Laravel app"
    url: https://www.mizouzie.dev/articles/how-to-test-protected-functions-with-phpunit/index.md
  - title: "Why Do My Pull Requests All Get Rejected?"
    url: https://www.mizouzie.dev/articles/why-do-my-pull-requests-all-get-rejected/index.md
  - title: "Laravel Live London 2026 Takeaways"
    url: https://www.mizouzie.dev/articles/laravel-live-london-2026-takeaways/index.md
  - title: "Code Block Click Copy"
    url: https://www.mizouzie.dev/articles/code-block-click-copy/index.md
  - title: "Tum | Modo | Postea"
    url: https://www.mizouzie.dev/articles/then-now-after/index.md
---

# If it doesn't make sense, build a little version

![shrugging builder](https://www.mizouzie.dev/images/build-a-little-version.png)


I have learnt a lot in the last few weeks and it was thanks to taking on a personal project that was a vastly scaled down version of what I have been working with this past year. What I have learnt was not necessarily fresh knowledge, but more an understanding that had previously evaded me. The reason I hadn't understood a lot of the concepts I was facing was because I had not built the original codebase to the original specs, I was just introduced to it in small sections and asked to work on individual features.

The "it" in question is an API that serves various instances of a different company blog websites. The frontend app can be included in the "it", but I worked predominantly on the backend. This backend is built on a [Laravel](https://laravel.com/) framework and uses [Twill](https://twill.io/) that was developed by the Area17 team. Both the framework and the add-on package are designed to make development quick and painless, which they do mostly, but can cause some upsets when out-of-the-ordinary requests start to come in from the clients. Stretching the capabilities of these things by having to hack the code that relies quite a lot on magic methods etc can be a cumbersome task.

A lot of the time that I was asked to customise something I was left scratching my head at the bugs I'd introduce and have to dive deeper than my limited understanding. It stretched me and taught me a lot, but not everything.

So I decided to make a little version for myself. A simple blog. API with a MySQL database backend, Nuxt.js frontend. Yes, I could have used one of the thousands of pre-existing "build-a-blog" tools out there, but I wouldn't be a true backend developer if I didn't take the complicated route 😬.

Starting out with the simple backend went great. I already had a repo that was the start of something with a decent set of models, routes and factories. I set up on a free hosting service that I could implement a (limited) MySQL database on, got familiar with the CLI for deployment and got on with it. Used the factories to populate the database and test my endpoints. All great.

Frontend was more unexplored territory for me. I'd dabbled in popular frameworks before and was comfortable with bog-standard HTML and CSS with a dash of JavaScript, but I'd never started a Nuxt app from scratch. So I did. The mistake I made was that I was referring frequently to a tutorial that was for v3, while building on the stable v2... I wasted about half a day before I realised so **that** in itself was a valuable lesson in what to pay attention to! After figuring that out and delving into the differences between the versions I had something acceptable looking in a short time and even had it pulling data from the already live RESTful API in an even shorter space of time.

I learnt a lot about making requests, CORS and props much better than when I'd previously "studied" them on various courses I had done. It also made me consider things about the overall structure of the API I had been working with before that had never occurred to me. This is what I meant when I said I wasn't learning **new** knowledge but establishing an actual understanding of what I had previously experienced. A lot of small "aaaaah ok, ok" moments.

The real mother-load of experience came from retro-fitting Twill into the already existing project on the backend. What I wanted it for was to allow me to add blog posts through a secure administration portal that would be totally separate from the public facing "blog page". The thing was, as with a lot of "easy to use" packages, it would have been a lot better to use it from the very beginning and build using it's almost auto-complete-esque CLI. It works by you telling it what modules you want and it builds your models, controllers and views for you. All you really need to do is minor customisations and tweak one or two configuration files. It really should be very easy, especially seeing as the documentation has **VASTLY** improved since I first tangoed with it. But because my models already existed I had to do some digging.

The benefit of *this* project is that it was a very basic version of a blog app, so it wasn't so daunting, so I got stuck right in and learnt a tonne about the inner workings of the tool and so much clicked about stuff I'd been baffled about months beforehand in the much larger work project. It was actually enjoyable and I found that by the second day I was actually looking for small problems so that I could delve deeper in yet another direction. I ended up doing some really cool stuff I never would have had the confidence to try before.

Overall, I am very glad that I did this and I am really looking forward to finishing it off and pushing the frontend live in the coming weeks because I really think I will feel genuinely proud of this once it's out there. I am a much much better developer because of this project because it elevated me closer to the level of the guys who built the monster project that used to frighten me, and they are very well established dudes.

I should also mention that I originally decided to do this when I realised that these articles that I write here on this platform (at the time: LinkedIn) are limited to be viewed only by people with a profile on here... so all the one's I've written previously will gradually be migrated over once the whole thing is live. I'm excited about it.

Moral of the story:

1. I was scared of the codebase I first worked on as a junior
1. I built a diddy version of it
1. I learnt that it really wasn't that scary
1. Even found a few areas that I could improve the scary codebase
1. Now I'm a way better developer

I strongly encourage developers to learn by building. If you're like me it is incredibly valuable and makes all the hours you've suffered in the past totally worth it.
