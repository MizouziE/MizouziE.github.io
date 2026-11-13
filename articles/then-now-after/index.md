---
title: "Tum | Modo | Postea"
url: https://www.mizouzie.dev/articles/then-now-after/
date: 2025-12-01
description: "A retrospective look at what I came from, assessment of where I currently am, and a prospective look at where I see myself going"
tags: ["web dev","learning","mental health","self-care"]
related:
  - title: "L.I.F.T - the anti-advice that could save you"
    url: https://www.mizouzie.dev/articles/l-i-f-t/index.md
  - title: "Re-reading my old blogs"
    url: https://www.mizouzie.dev/articles/re-reading-old-blogs/index.md
  - title: "Recognise struggle for what it is"
    url: https://www.mizouzie.dev/articles/recognise-struggle/index.md
  - title: "Starting is one thing, STOPPING is another"
    url: https://www.mizouzie.dev/articles/know-when-to-stop/index.md
  - title: "I Tried Working at 30,000ft"
    url: https://www.mizouzie.dev/articles/tried-working-at-30000ft/index.md
---

# Tum | Modo | Postea

> **_tūm_** *(pronounced /tuːm/)* — “then,” “at that moment”; here, a glance back at what once was and the moments that shaped the beginning.  
>
> **_mŏdō_** *(pronounced /ˈmɔ.doː/)* — “just now,” “only now”; a grounding in the present, the point of clarity from which everything is assessed.  
>
> **_postĕā_** *(pronounced /ˈpɔs.tɛ.aː/)* — “afterward,” “later on”; a forward-looking view toward what may unfold from this moment onward.

Given that I've re-kindled my interest in this little blog of mine, and that it's almost Christmas, it seems a fitting time to get a bit Charles Dickens and have a look at my past, present and future. Part of the reason I have picked this thing back up is that I finally feel settled in the job I've been at for a few years and actually have a bit of time now because I'm not constantly trying to catch up with everyone. Not that having been in a catch up loop was a bad thing, it was nothing short of a huge opportunity to learn a **tonne** and really add some serious strings to my web developer bow. I think it's going to be a very worthwhile exercise to look back at where I was when I started out, honestly assess where I'm at currently and then see what avenues I think Id like to go down in the not too distant future.

## Then

Back in November 2023, I took my first flight back to the UK _for work_ to attend my initial training for a job at Fusions PIM ltd. I had already been working in web development and with Laravel for a couple of years, so wasn't totally green. My CV at the time listed core skills like:

- Laravel (2 years experience)
    - Built customer requested features into an existing project
    - Built a CRM and knowledge base for a subscription based retail company from scratch
- Docker
    - Comfortable working in development containers
    - Hosting dockerised web applications on bare servers
    - Using containers to "try out" different elements of tech stacks
- Open Source
    - Started and managed a small web crawler project aimed at offering beginners an opportunity to try out OSS

In fairness, I'd not made a bad start considering my first experience with any of this world was when I did [my first Laracasts tutorial](https://github.com/MizouziE/birdboard) in 2021. But my actual real world experience, and impact on the actual industry would not have even registered as negligible. Looking back I'm a little astonished that this long established company took a punt on me, but I'm very glad they did.

What I lacked in experience I tried my best to make up for with soft skills like clear communication, not being afraid to look a bit daft by asking a silly question and apart from that just trying really hard to up my levels constantly. I'd been lucky before where I had worked in that I had a huge amount of freedom to manage myself and my workload, and that did not change when I arrived here, even though the company technically _had_ to be more "organised" to allow for customer transparency and coordination. So I'd work on what I needed to work on, I could literally ask any one of my new colleagues for advice or help as and when I needed it and I still had time to just read and read and read. The reason this reading experience was so valuable was that the codebase I as now working in had been around since long before I'd learned even a single function of PHP. Some of the classes had syntax from years ago. A true legacy monster. Even though a great deal of it had been already converted into a Laravel app, there were still plenty of old quirks that were too fundamental to the business to risk chopping and changing them, so they were just as they were. It was kind of like being locked in a time-rift, but that had more benefits than I realised at the time.

Some context of the conversion in progress;

- The application had already had a very similar structure to that of a Laravel app from the start
- Many of the Symfony components that Laravel is built on top of were already being used
- The parts that differed most in the application were to do with more old fashioned security concerns

## Now

Skip ahead a few years and that same mysterious and enormous PHP application that was half-of-yesteryear has been converted almost 100% to a modern Laravel app, through the efforts of colleagues old and new and of myself. The main takeaway I had from being a part of this conversion was that I had to gain a deeper understanding of the Laravel framework because the parts that were left to convert were the more challenging ones, which makes sense as they'd have been bonkers to have not started the conversion before I got there with anything other than the easier parts. I think the areas I got my teeth into the most were Eloquent and Commands, but I definitely was routing around in almost all areas of the framework codebase.

### Eloquent

A lot of the older parts of the codebase I was working on used proper old school PHP syntax of ``$sql = 'SELECT * FROM `some_table` WHERE `x` = 'y';`` so I got lots of practice reading and understanding SQL, the re-writing to get the same results using eloquent queries. Not only that, much of the application was to do with search filtering based on model relationships so I had to make sure I really understood how relations could be properly defined and used for querying. Over the years I was doing this, eloquent methods for querying relationships were improving week on week and I was constantly being presented with ways to improve what we had, both for performance **and** for developer experience.

The main thing I love about eloquent is how well it reads, especially around relations. A lot of the time there's no need to _think_ too hard about what is happening because the methods, like `withWhereHas('relation', fn ($query) => $query->condition())`, tell you exactly what the result is going to contain.

### Commands

My appreciation for Symfony knows no bounds because I've really been in the [trenches of the docs](https://symfony.com/doc/current/console.html) for _just_ the console and it's pretty awesome when you consider how fundamentally simple it's been kept. In my humble opinion, Laravel has obscured _some_ of the simplicity in the parts they've borrowed, for instance when you combine the testing helpers `$this->artisan(Command::class)->expectsTable([...])`, which makes a huge assumption that you've defined the table styling, with a command output table in which you've not defined the style for you'll be momentarily stumped why your tests are failing before figuring out that the default style for tables in Laravel is not the same as the default style for tables in Symfony :face_with_diagonal_mouth: 

Apart from the small quirks, the capabilities of commands is fantastic and it even inspired some thoughts in me that lead me into the speculative part of this assessment that will be explored a little later.

### Overall

Having spent this time writing code, as well as reading and reviewing a lot of code of high quality, my style and even thought process when solving problems has improved a great deal. I am happy with where I'm at currently, but can certainly see the edges of my own scope for improvement and will continue to chase after it. I've kept up with practicing outside of work with things like [CodeWars](www.codewars.com) and tried to maintain my interest in languages outside of my 9 to 5 use of PHP and SQL, mostly bash and Golang but also recently delving into C++. Also, because I've recently reached this comfort level I've picked up this small side project and hope to be able to maintain writing again, as it definitely did contribute to me developing my own understanding back when I started out.

Contributing to open source projects has become a more regular thing recently, which has boosted my confidence somewhat as well as been a great opportunity to see how different, well established projects work and some experiences resulted in positive changes in my day to day work.

Having reached this level has afforded me more time to pursue other old hobbies like reading too, which has been great for me personally and has inspired a new dimension of this very personal website that I'm quite excited to get launched soon (Look out for a [tl;dr](/../tldr) section up in the navigation!).

## After

So seeing as I think that I managed to reach the "next level" that I'd envisaged when starting out at my current position, what comes next?

I have had experience of working under some great development managers, and I think that I should (and will) aim for not their position, but their mentality. I believe the next major improvement that I can make is being able to think X steps ahead and in a big picture sense, regardless of how cliché that sounds. I mean, things _become_ cliché when they're tried and true, no?

What I mean is that I hope to improve my decision making when facing a problem, or writing a feature. I have gained a decent amount of experience and should be able to leverage that into foresight, while still building and reinforcing that experience. I have seen first hand how all decisions (in a architectural sense) are about trade-offs as there's never a truly 100% correct solution, just a best-fit one, or even two. I want to be able to weigh up the pros and cons with confidence and then execute plans based on those decisions. I even have a few examples of this type already in my sights, so I want to be sure that I am pursuing them with a level head and not for the sake of wanting to "establish" myself or anything selfish like that.

Another thing I hope to develop in myself is to keep focused on what matters in a business sense. I've been guilty of spending too much time writing things "the right way" when the actual business value of the thing I'm writing is small. At the end of the day, we build tools to solve business problems. When we solve the problem, we gain value, and making the code clever, or pretty doesn't actually make as huge of a difference as we feel like we're making while doing it. A [great video explaining this](https://www.youtube.com/watch?v=RfRp6CwKoVU) from 2025 Laracon EU by John Drexler, which my team and I were actually in attendance for, sums it up perfectly and even though my teammates and I constantly refer back to it, I know for a fact that I can still implement the mentality much better than I do now. By doing this more strictly I will greatly improve my value as a developer.

So after that brief look backward, inwards and forwards, I think I am mostly grateful for the opportunities I have been presented and I am actually pleased with what I have managed to achieve with them, which definitely does not align with how I felt back then :sweat_smile:

I could probably be a little easier on myself, because having struggled with things did not mean I was as dumb as I felt!
