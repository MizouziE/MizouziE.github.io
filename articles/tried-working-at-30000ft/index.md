---
title: "I Tried Working at 30,000ft"
url: https://www.mizouzie.dev/articles/tried-working-at-30000ft/
date: 2023-07-05
description: "The rare occasion of me traveling alone came up and I was excited to have a few hours alone with my thoughts and make the most of the opportunity to get loads of work done."
tags: ["web dev","self-care"]
related:
  - title: "Tum | Modo | Postea"
    url: https://www.mizouzie.dev/articles/then-now-after/index.md
  - title: "L.I.F.T - the anti-advice that could save you"
    url: https://www.mizouzie.dev/articles/l-i-f-t/index.md
  - title: "Recognise struggle for what it is"
    url: https://www.mizouzie.dev/articles/recognise-struggle/index.md
  - title: "Starting is one thing, STOPPING is another"
    url: https://www.mizouzie.dev/articles/know-when-to-stop/index.md
  - title: "Laravel Live London 2026 Takeaways"
    url: https://www.mizouzie.dev/articles/laravel-live-london-2026-takeaways/index.md
---

# I Tried Working at 30,000ft

![What I though it would be like](https://www.mizouzie.dev/images/plane-dev-2.png)


## How I thought it would go

I had always seen people working on their laptops in airports and on aeroplanes, so it was an easy assumption to make that I too would be able to do so. I have two projects on-going and definite _next steps_ that need to be done for each. Fairly monotonous tasks that I should be comfortable with and can just get on with.

Normally, when working at home I only get about 2.5 hours in the day when I am 100% left alone and can focus fully on a task. This golden hour is great, but I'd love to know how productive I could be given the opportunity of a longer window of focus. Stuck in a seat on a plane, without having to entertain my 6 year old son for at least 3 hours with the potential of an additional hour either end of waiting around. Sounds like a great chance to find out!

I usually work on remote development servers so that was something to consider. There's no WiFi at 30,000ft in economy class. But, as I said, my tasks were not anything new to me, so as long as I had my repository saved locally I should probably be ok. I've written hundreds of tests in the last few months alone, and I need to write some for a fresh project so they'll be super basic. Should be a blast. Alternatively I've got some HTML to steal from a template email and incorporate into some usable components for another project. Low level stuff.

## Preparations

So my main concern was that most of my current work is remote, so first thing to do was to `git clone` everything I intended to work on into a local directory. Simple enough.

The projects are Laravel applications, so I made sure to set up my `.env` and run `composer install && npm install` so that all my dependencies were available. Luckily I had an inkling to be extra cautious and tried to run my migrations to make sure the MySQL database was all ok. I say luckily because I didn't actually have MySQL or MariaDB installed!

So I went about installing MariaDB as I had always just used docker for my databases when working locally before moving over to a remote development server. For some reason I have gone off of Docker lately so prefer to just run MariaDB (the OSS and friendly version of MySQL) on my machine. I did write about running databases in Docker before [which you can read by clicking here](/articles/why-use-docker-for-databases-and-how).

I made sure to have my laptop battery, phone battery and headphones battery all fully charged and even made sure to have some good old fashioned mp3s saved on my phone so I could still listen to music without streaming.

For the project that I need to convert some HTML to components, I made sure to save a copy of the HTML from the page where you can preview the email. Even went s far as to check that I could see it lal properly when serving just my single stolen page. And it worked just right immediately.

## How it really went

Safe to say nothing went like I imagined...

### Project #1

This is a work project, fresh Laravel app that I spent the previous few days designing and laying out the models and database structure for. I had to start some basic tests as I'd kept everything fairly loose while figuring out what kind of relationships the models all needed between them. Now that I was more settled, it was time to write the tests to ensure that further changes don't break things.

So I write my first test, easy enough. Goes green after not too many errors. Second test the same. Then I want to refactor early to avoid repeating myself in populating the database with data and an authenticated user. I'd done this before I'm sure, what I believe I need is:

```php
public function __construct(
        public User $user,
        public Client $clients,
) {}

public function setup(): void
{
        $this->user = User::factory()->create();
        $this->clients = Client::factory(50)->create();
}
```

But it turned out that this isn't what I needed. I am somehow passing a string to the typed variable... as I sit here now, still on the plane I cannot put my finger on how I am doing this wrong, but I know that if I had an internet connection right now all I need to is check a repo I worked on a week or two ago (on the remote server) and I'll see immediately how I wrote the **actual needed thing** rather than this terrible recollection/representation of it.

I know it's possible to run a `setup()` method to run what ever seeding you want or create a user that will be _reusable_ between tests. I know because I did it recently. It's very frustrating that I can't recall it right now.

> Edit, the following day: I found what my problem was in the docs. I should have used `setUp()` with an uppercase "U" and also called `parent::setUp()` within my method. I found it at the bottom of [this section about testing](https://laravel.com/docs/10.x/testing#creating-tests). What I actually needed was the following:
```php
    private User $user;
    private Collection $clients;

    public function setUp(): void
    {
        parent::setUp();
        $this->user = User::factory()->create();
        $this->clients = Client::factory(50)->create();
    }
```

I could just repeat myself for the moment and come back to refactor later having referred to the docs or even my older code. So let me write some tests using the `$this->actingAs($user)` to make sure I wasn't getting redirected to a login route. Oh, there is no login route. I had only installed Breeze, Laravel's quick and easy solution to out-of-the-box auth, on a branch that got scrapped after a front end re-design. Great... No matter, even though I cannot quickly download and install Breeze again without internet, I can just `git cherry-pick` the commit from the abandoned branch on which I had previous installed it. As it is mostly new files and my current `routes/web.php` file is untouched, then the changes brought shouldn't break anything.

I impressed myself that I managed to do this without Googling anything, if I'm honest. It worked. The thing I did not anticipate was all the changes to tailwind that would require another `npm install`... damn it. My front end was now broken and I could not run `npm run dev` without it crashing. I had a hacky way of circumventing this that involved commenting out some lines in the `postcss.config.js` file but it was ugly, and I even tried just abandoning the `middleware('auth')` on the routes altogether to just write some tests but I was pissing in the wind at this point. Move on Sam.

Never mind, I have a backup for this situation.

### Project #2

This is  a project I'm working on for a friend that is a partly automated email sending app. I have already written all the guts of it and it sends what we want and collects email addresses etc and that's all great. Now it just needs the front end polish, which I typically hate, but He made a template using MailChimp email builder and all I had to do was sift through it and pick out the various parts and styling that we wanted in our email.

I had glanced at the HTML when I copied it and it looked ok-ish. But on closer inspection it was a straight up mess. Toxic even. Style attributes in almost all HTML tags. The whole thing was one big table with forced layout sizes. Not only that, the entire `<body>...</body>` was written on  a single line and even with my autoformatter, I could not get it into a sensible layout, so picking out bits and editing others was a horrendous amount of side scrolling.

Oh and did I mention that the kid sitting in the seat in front of me was some kind of jack-in-the-box? Every time he jumped, my thumb would accidentally swipe over the tracking pad and loose the place I had just painstakingly scrolled to. I tried working with the laptop on my actual lap, as the name may suggest is easy to do so, but it's a poxy little 14" screen so it was very uncomfortable. Did not help that the style of plane seat arrangement was inline with a tin of sardines. No legroom whatsoever       .

## Lessons learned

So absolutely nothing that I planned to get done got done. Zero for two. Instead of letting myself get mad about it, I decided to write this so that I could at least gain _something_ from this experience. The main lessons I learned were:

1. Developing a Laravel 10 application without an internet connection is tough if you've not got all your packages inline beforehand.
1. Even doing tasks that had been done repeatedly before can sometimes need the littlest bit of help from some reference, be it documentation or an old project.
1. Before doing things totally offline, doing a dry-run is probably a good idea to catch the trivial hurdles.
1. HTML generated from "builder" style tools can be clunky and follow anti-patterns.
1. I am better at git than I used to be. (Maybe because I messed it up so many times!)
1. Cheap flights do not have a comfortable amount of room to think in.
1. It's better to write content than code in my case, because I am still too dependant on documentation.
