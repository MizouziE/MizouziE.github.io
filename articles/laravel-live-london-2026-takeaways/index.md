---
title: "Laravel Live London 2026 Takeaways"
url: https://www.mizouzie.dev/articles/laravel-live-london-2026-takeaways/
date: 2026-06-20
description: "A jam-packed couple of days with incredibly valuable talks throughout. Here is what I got out of it all."
tags: ["laravel","web dev","PHP","career","Community"]
related:
  - title: "Why Do My Pull Requests All Get Rejected?"
    url: https://www.mizouzie.dev/articles/why-do-my-pull-requests-all-get-rejected/index.md
  - title: "How to test protected functions with PHPUnit in your Laravel app"
    url: https://www.mizouzie.dev/articles/how-to-test-protected-functions-with-phpunit/index.md
  - title: "Undefined type 'Spatie\\Permission\\Models\\' . 'Permission' | 'Role' | 'HasRoles'"
    url: https://www.mizouzie.dev/articles/undefined-type-spatie-permission/index.md
  - title: "Send SMTP Mail from Hetzner Server with Firewall"
    url: https://www.mizouzie.dev/articles/send-smtp-mail-from-hetzner-server/index.md
  - title: "3 things the best senior developers do"
    url: https://www.mizouzie.dev/articles/best-senior-developer/index.md
---

# Laravel Live London 2026 Takeaways

![laravel live london 2026](https://www.mizouzie.dev/images/laravel-live-london-2026.png)


I had the pleasure of attending the 2026 edition of Laravel Live this week just gone and it was nothing short of an entirely enjoyable experience. Probably mostly because I got to spend some time with my colleagues, who I don't see too often seeing as there's about 3000km between us, but certainly for a number of other reasons which I'll try to lay out for you now.

## Quality and Quantity

They really seemed to hit the nail on the head in both of these areas. There was a great balance of how many talks there were (16 over the two days) and the quality of the speakers' subject delivery. I felt that I came away having learnt something at the end of each day, even though a lot of what was talked about were things the project I work on has been implementing lately.

## Validation

Many of the talks focused on some perspective and advising and best practices in modern day Laravel development, which it turned out the [team I work on](https://www.fusionspim.com/team) has been putting into practice over the last 18 months or so. So that was like a huge pat on the back, especially seeing as we've been working pretty hard and we've all certainly felt it. I think we each had a bit of a "yeah, the hard work was worth it" moment when we realised that we're ever so slightly ahead of the curve in general.

One of the most interesting _patterns_ that became apparent was that security and AI can work well hand in hand when coupled with **determinism**. That determinism is most easily achieved by making use of tools like PHPStan/LaraStan for static analysis and Pint/CodeSniffer for style and formatting, which we've leaned more and more into, along with a high level of automation in our development workflow and it really has had a hugely positive impact for us all.

## My Notes

![The only notes I took over the two days](https://www.mizouzie.dev/images/laravel-live-list.png)


You'll be able to find a list of the speakers and their talks on the Laravel Live London website. I'm not sure it was recorded though, so I'm not sure if they'll ever be available. But the above are the parts that jumped out most for me, so I'll break them down here:

### Migrations
#### Expand and Contract 

Ryan Chandler gave a great talk on optimising workflows and shipping to production and this talk was one of the most validating for us as **many** of the principles given as good examples are exactly what we do. The best and most enjoyable one was that even at the head offices of Laravel themselves they don't agree with actually using the `down()` method in migrations! :joy: We, and I'm sure many others do too, completely agree and have our custom stubs without all that noise in the `php artisan make:migration` command output.

The part of the talk that was only semi-validating, but more of a _this is the proper label for that way of working_ so was worth writing down was the label of "Expand and Contract Migrations". It's best explained with an example:

Say you have to change the name of a column in your database. It could be done very quickly and easily using available [schema facade methods](https://laravel.com/docs/13.x/migrations#renaming-columns), but it's not the safest and most reliable way of going about it when it comes to the prod database. It would be far safer to make use of feature flagging (which we do... all the time) and instead of renaming, creating a _new_ column and copying the data over in migrations #1, then once the feature flag is enabled on production and confirmed to be running safely, use a second migration to drop the old named column as it's no longer referred to at all.

Much safer, should anything unpredictable happen, and not much extra work. Solid practice.

#### SafeMigration

This is lifted straight out of the slides, which are [readily available at](https://optimising-for-developer-efficiency.on-forge.com/). It is a wrapper class that enforces a 15 second lock wait timeout for the types of migrations that can potentially block a production database and ultimately kill the live app.

```php
class SafeMigration
{
    public static function run(Closure $callback, int $timeout = 15): mixed
    {
        $original = DB::scalar('SELECT @@lock_wait_timeout');

        DB::statement("SET lock_wait_timeout = {$timeout}");

        try {
            return $callback();
        } finally {
            DB::statement("SET lock_wait_timeout = {$original}");
        }
    }
}
```

It uses a try/catch pattern to set the database session's lock wait timeout to a given or default 15 second limit, attempts to run the callback which will fail if not complete within the limit or pass, and then finally puts the timeout back as it was.

He went on to suggest pairing this with a custom PHPStan rule to enforce destructive or potentially lengthy migrations all use this wrapper. Smart.

### Testing
#### Fake Services

During tests, it's very common practice to reach for Laravel's helpful facades for their faking ability, especially `Http`. Over time the setup for these faked calls can become boilerplate which means more and more places to update when a service implementation is slightly altered. When it's the same boilerplate used over and over, it makes sense to move that all over into a single service class and use a ServiceProvider to mount that service on the container when testing so that the code you are testing just calls that spoofed service and gets its expected output without needing to litter the test with all that setup.

>We can even go a step further and re-use the service class in `local` development so that even when a new starter is still in their probation phase, they can have some kind of working application without needing API keys and service licenses.

#### Closed (as in Open-Closed)

This was a validation of something I had noticed and considered a code smell in a few places over the years. When reading a PHPUnit test class and you see a method named `test_does_not_do_x()` then it is signalling that the class under test is not properly **closed**. By closed, I mean the latter part of the good old _Open-Closed Principle_ which is a part of the **SOLID** design principles.

Yannick Chenot gave a great talk where he demonstrated just how much less cognitive load a developer suffers when the classes they work with a well designed according to these principles, and the part that stuck out for me was the demonstration of how a properly closed design negates the need for these _negative_ types of tests that tend to creep in with bug fixes.

Next time I come across one I will definitely be looking into fixing the root cause and seeing if I can't just improve the underlying class, or at the very least write up a decent GitHub issue to come back to that part and rectify the design errors.

#### Eloquent Factories

Another thing my team has definitely discussed in the past was making use of the [Laravel magic methods](https://laravel.com/docs/13.x/eloquent-factories#has-many-relationships-using-magic-methods) for factories when defining relationships and we settled on the idea that converting them all would be a monumental task in our 4000+ test strong test suite, so we'd just adopt the practice going forward. We agreed that they just read easier as often, the details of the related model are of little significance, and the full-bodied nested factories can sometimes be a bit of a distraction when reading the code.

However... a strong, recurring theme over these two days was "use Rector", which we technically already were, but we were not quite _utilising_ Rector's exceptional power, or that of old Claude-bot-boy either. So the note here was to remind me to have a crack at making a custom Rector rule to enforce the use of these magic methods when the related factory was simple enough. At time of writing this, I have a PR that me and Claude worked on today to bring in such a rule, and it was challenging to say the least. I hope it goes down well in review, and if it does and we can improve on it a bit, who knows? Maybe we can try and submit a real rule out in the wild (It works quite well, but currently doesn't format things ideally).

I have to give great credit to Dave Liddament for how well he explained the inner workings of Rector rules. It was literally like the moment the curtain was drawn back which hid the Wizard of Oz and I could see clearly, "Oh, it's just a bloke... I could do that!".

### Value Objects

The talk given by Harris Raftopoulos was a favourite of mine, mostly because I've spent a large part of the last year and something converting things into enums since their massive enhancement with the release of PHP 8.4 at the end of 2024. I think they're great. Enums, I mean. However my mind was blown when the simple idea of Value Objects was explained in front of me and I realised it was **everything** I still felt was missing from my beloved enums. I had three distinct places in mind in my current work project that had not been converted because it felt like something was just missing, but the added depth that using a value object brings addressed all my concerns.

#### Validation

It's easy to include built in validation in the constructor, meaning we can create the beautiful _single source of truth_ that all developers love. What it means is that, if your application allows for CRUD being done with models via a typical edit form, it should be using a `FormRequest` class for validating the user input, but if the application _also_ allows for bulk updates via some sort of file (.csv, .tsv etc) ingestion then if your validation logic lives inside a Value Object for the fields coming in, that validation can be shared equally between user input and file upload. Chef's kiss.

#### Casting

Another super-power of enums is using them as castables on your models. It helps to ensure predictability and often simplifies data manipulation in places throughout. You can also cast to these magnificent Value Objects too! So you can enjoy all those benefits combined.

### Static Analysis

As mentioned multiple times, static analysis tools were mentioned _multiple times_. Another thing that we were kind of already doing, but we were not quite PHPStan-maxxing like we could have done. At least 50% of the talks gave one or more fantastic examples of how to deterministically ensure code quality, and it goes hand in hand with the ever more popular AI assisted coding as it helps reduce that space for errors. A principle which was explained in great detail by Ashley Hindle, who may very well have seen the future.

One of the most interesting mentions around static analysis was of the existing rule sets that are out there in the community. Laravel Octane has a set of rules because it has a particular way of working on a server, and it turns out that the app we work on day in, day out at work shares a lot of similarities with an app on Octane because it's technically a legacy app that has been dragged, kicking and screaming, into Laravel 13 (and 10, 11 & 12 along the way) so it still has a few "old" bits here and there, so we need to worry about things like static caching. Turns out, there's a rule for that!

### Hidden config

As Laravel is built on top of things that are built on top of things, there are more than a few configurations that are "undocumented" from a Laravel developer's perspective. Liam Hammett gave a talk listing 50 tips for working with Laravel, and I can proudly say we do at least 43 of them already.

One of the cooler ones that I had no idea about was [this gem](https://github.com/filp/whoops/blob/master/docs/Open%20Files%20In%20An%20Editor.md) buried in the docs of `filp/whoops` which powers those helpful debug screens we see in local development (and NOT in prod, right? :wink:). It simply allows you to set which code editor you're using as an environment variable so that you can click a link on the debug screen and it opens on the file and line of the error. Great time saver!

### NativePHP

I remember seeing the talk about this nearly two years ago at Laracon EU in Amsterdam and it seemed like a cool project, but the idea of making a Native desktop or mobile app using a Laravel framework did not seem entirely serious. I ate my ~words~ opinions after seeing the talk about its current state though. It's certainly worth your time looking into if you are a developer who works with Laravel. My only gripe is now I can't really say to friends that hit me up with their "app idea" that mobile apps aren't my thing.

### Unless

This wasn't really mentioned as a topic, but somewhere a code example used featured the `unless` keyword, which had recently been a topic of contention in my camp where I am very much in favour of using it, but my colleagues were quite against it. During a brief, whispered re-ignition of the discussion after seeing the code, I was sent [this article](http://www.railstips.org/blog/archives/2008/12/01/unless-the-abused-ruby-conditional/) as a final proof that I was crazy, but it genuinely just convinced me that I was right. You be the judge.

## Overall

The gist was;
- Keep doing what you're doing
- Do _more_ of what you're doing
- Determinism makes AI usable

It was a good conference, and I'm glad I got to attend. I also appreciate all the work that goes into the talks, but I sincerely hope that those who do that work know what a positive impact they are having on the wider community and continue to do so. I'm already looking forward to the next one!
