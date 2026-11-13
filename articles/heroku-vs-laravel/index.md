---
title: "Heroku vs Laravel (vs me...)"
url: https://www.mizouzie.dev/articles/heroku-vs-laravel/
date: 2022-04-26
description: "Deploying a Laravel app on a (then) popular free hosting platform proved tricky, so here are the steps that no-one told me."
tags: ["web dev","heroku","laravel"]
related:
  - title: "Why Do My Pull Requests All Get Rejected?"
    url: https://www.mizouzie.dev/articles/why-do-my-pull-requests-all-get-rejected/index.md
  - title: "Laravel Live London 2026 Takeaways"
    url: https://www.mizouzie.dev/articles/laravel-live-london-2026-takeaways/index.md
  - title: "How to test protected functions with PHPUnit in your Laravel app"
    url: https://www.mizouzie.dev/articles/how-to-test-protected-functions-with-phpunit/index.md
  - title: "Undefined type 'Spatie\\Permission\\Models\\' . 'Permission' | 'Role' | 'HasRoles'"
    url: https://www.mizouzie.dev/articles/undefined-type-spatie-permission/index.md
  - title: "How to Unlock Formkit Multistep forms on a Laravel Inertia app with Vue 3"
    url: https://www.mizouzie.dev/articles/how-to-unlock-formkit-multistep-forms-on-laravel-inertia-app-with-vue-3/index.md
---

# Heroku vs Laravel (vs me...)

![Heroku (Heihachi) against Laravel (Kazuya)](https://www.mizouzie.dev/images/heroku-vs-laravel.jpeg)


The first time I attempted to deploy a Laravel project onto the popular hosting service Heroku I did not get very far. I found a number of helpful tutorials just with a few google searches, but none of them actually got me across the finish line. It was my first actual attempt at "going live" and the whole thing left me somewhat bemused and painfully aware of my lack of understanding. Both Laravel and Heroku come with ample documentation, but it may as well have been written in a foreign language as I could not make heads nor tails of it.

Today, thankfully, was a different story.

Only a few weeks after my first failed attempts, I had been away and deployed a much more simple static site version of the failed project, [which you can see here](https://enkle-word-game.github.io/enkle/), using GitHub Pages. It wasn't very difficult to transfer my blade template over to an `index.html` file and make the necessary changes and imports of the JavaScript files that made it all tick. That in itself was a nice learning experience, but still left my Laravel-Deployment itch very much unscratched.

I had a recent personal project on the back burner that was in danger of fizzling out, so I figured I had nothing to lose by firing it up again, trying to remember where I left off, making it presentable to a degree and then having one more crack at Heroku because I had read in a number of places that it was very much possible to run a PHP web-app on a framework like Laravel. The difference now was that I had gone up a level or two in my search engine ability and that made all the difference. I had hit a number of stumbling blocks in my first run and they'd worn me down to the point I was unable to overcome the last few. I went right ahead and got to that same point, only this time more energized.

I am using [Laravel 9](https://laravel.com/docs/9.x) with [Tailwind CSS](https://tailwindcss.com/) and deploying via the [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli).

Here are the bits that were harder to find out about than the obvious googles:

![Procfile containing the string "web: vendor/bin/heroku-php-apache2 public/"​](https://www.mizouzie.dev/images/heroku-vs-laravel-01.png)


The `Procfile` not only has to be capitalised, but MUST be present in and pushed to the heroku master branch **from the local master branch**. Being a fan of git and not a huge fan of Heroku, I always made heroku changes on a separate branch and pushed from there but continually saw "no Procfile found" and then that lead to the app not being run from my public/ folder on the route... which was **clearly** specified in the Procfile.

![config/logging.php where value for the key "driver"​ has been changed to "errorlog"​](https://www.mizouzie.dev/images/heroku-vs-laravel-02.png)


Set the logging to "errorlog" so that Heroku can read and display the application's logs through both the CLI and the GUI on their handy dashboard.

![Laravel docs showing asset URL configuration](https://www.mizouzie.dev/images/heroku-vs-laravel-03.png)


Tailwind will throw a curveball if your stylesheet reference is the same as the above, and I found the reason for this in the console of the browser. When I was looking at my "deployed" site it just seemed to be a garbled mess. I was so happy to no longer be getting 500 errors or 403 errors... but what I was looking at tainted my happiness. On inspection, it seemed that the CSS files were being called for, but on "http://" rather than "https://" and were therefore blocked. Heroku automatically makes your main page's root securely accessible, but it isn't automatic about anything else. You'll need to set a previously non-existent variable in the .env file on the server. This can be done painlessly in two ways. Either through the CLI with the command `heroku config:set ASSET_URL=<insert your root url here>` or if you already have the heroku dashboard open you can view settings and click on "reveal config vars" where you have a place to add/edit as many variables as you need.

They were the final pieces to my puzzle that were not apparent to me the first time. I hope this list of them can save someone the pain I felt when I couldn't get it working before. At the very least, I hope that by writing it all out and going over the process once more I, myself, might have learnt some kind of valuable lesson. Time will tell.

In the meantime, why don't you check out the micro nutrient tracking site I am **still** developing and let me know what you think of it. (*Editor note 2023-01-21: Of course Heroku is no longer free so this app is not live any more, sorry!*) If anything is a particular pain point then I'd be very open to receiving issues or even pull requests on it's [github repo](https://github.com/MizouziE/Minerals/issues).

Thanks for reading, and if you got the Tekken reference at the top of this article you NEED to send me a DM or email or something, we're basically already friends.
