---
title: "Developing a Laravel Application using Tinker"
url: https://www.mizouzie.dev/articles/developing-a-laravel-app-using-tinker/
date: 2023-02-08
description: "The Laravel framework has a huge amount of helpful features, let's take a deeper look at one of the most powerful. Tinker."
tags: ["web dev","laravel","productivity"]
related:
  - title: "Why Do My Pull Requests All Get Rejected?"
    url: https://www.mizouzie.dev/articles/why-do-my-pull-requests-all-get-rejected/index.md
  - title: "Laravel Live London 2026 Takeaways"
    url: https://www.mizouzie.dev/articles/laravel-live-london-2026-takeaways/index.md
  - title: "How to test protected functions with PHPUnit in your Laravel app"
    url: https://www.mizouzie.dev/articles/how-to-test-protected-functions-with-phpunit/index.md
  - title: "Undefined type 'Spatie\\Permission\\Models\\' . 'Permission' | 'Role' | 'HasRoles'"
    url: https://www.mizouzie.dev/articles/undefined-type-spatie-permission/index.md
  - title: "3 things the best senior developers do"
    url: https://www.mizouzie.dev/articles/best-senior-developer/index.md
---

# Developing a Laravel Application using Tinker

![Laravel Artisan Tinker](https://www.mizouzie.dev/images/laravel-artisan-tinker-command.png)


## What is Laravel Tinker?

The short answer is that it's a super power they give you when you boot up your Laravel application. All you type into the terminal:

```sh
./artisan tinker

OR EVEN...

./artisan tink
```

This command boots up the built in REPL that is based on PsySH. REPL stands for; **Read** the user input, **Evaluate** the input, **Print** the resulting output and **Loop** back again to await further input. What kind of input and what kind of output though?

### Input

The Tinker shell takes good old PHP. It's like a little Laravel engine so it has every function within the framework at it's disposal. It makes running logic wonderful thanks to how "fluent" the Laravel function naming is, you feel like you're casting spells by speaking things into existence.

### Output

Typically you're going to use Tinker for database interactions. You will be calling for eloquent collections or manipulating records on the database. When calling for eloquent collections, they'll be displayed with all relevant information in a format similar to JSON. When making alterations to records, it will give you `true`, `false` or an error message.

## How does Sam use Tinker?

I imagine that I use Tinker in a similar way to most. The most common use case is when I am building controllers. Seeing as that is usually where the business logic lives. It is like my test-dummy. When I need to perform weird and wonderful things with my models outside of the bog-standard CRUD stuff.

### Proofing

Typically, I'll use it to test out and confirm that I've use the correct relationships in my models. For example:

```php
<?php

namespace App\Models;

class User extends Authenticatable
{
...
    public function brand()
    {
        return $this->belongsToMany(Brand::class);
    }

    public function sites()
    {
        return $this->hasManyThrough(Site::class, Project::class);
    }
}
```

With a model like the above, some things I could run would look like:

```sh
sam@MizouziE:~/code/laravel$ ./artisan tink
Psy Shell v0.11.8 (PHP 8.1.2-1ubuntu2.10 — cli) by Justin Hileman
>>> User::first()->sites()->count()
=> 76

>>> User::first()->sites()->find(13)
=> App\Models\Site {#4797
     id: 13,
     site: "Rebeka",
     url: "http://altenwerth.com/occaecati-magnam-molestias-hic-aperiam-qui-non-modi",
     competitor: 1,
     project_id: 2,
     created_at: "2023-02-02 10:19:35",
     updated_at: "2023-02-02 10:19:35",
     brand_id: null,
     region_id: null,
     match_count: 0,
     opportunities_count: 0,
     gaps_count: 0,
     filename: null,
     laravel_through_key: 1,
   }

>>> User::first()->brand()->first()
=> App\Models\Brand {#4803
     id: 1,
     brand: "NU BALANCE SPORTS",
     key: "NBS",
     created_at: "2023-01-20 12:02:50",
     updated_at: "2023-01-20 12:09:45",
     pivot: Illuminate\Database\Eloquent\Relations\Pivot {#4784
       user_id: 1,
       brand_id: 1,
     },
   }
```

So the code entered for the input is just your run of the mill eloquent, and this is exactly how I confirm that the functions I write in my controllers are doing exactly what I want them to do. It saves me loads of time debugging when I forget to end my chains with a `->get()` or something trivial like that.

### Tweaking

Another way I use it is to check that I get the desired results from a function in the database. I will often have the console open and a the table I wish to alter side by side, set a few variables in the Tinker session and copy + paste commands out of my controllers to ensure that the desired changes occur in my local development database. I find it best for spotting when I get relationships mixed up between `belongsTo()` and `hasOne()` or when I should have created a pivot table for example. This is facilitated by the excellent feedback you get from the error messages.

```sh

>>> Brand::whereHas('projects')->with('sites')->where('user_id', 1)->get()
Illuminate\Database\QueryException with message 'SQLSTATE[42S22]: Column not found: 
1054 Unknown column 'user_id' in 'where clause' (SQL: select * from `brands` where 
exists (select * from `projects` inner join `brand_project` on `projects`.`id` = 
`brand_project`.`project_id` where `brands`.`id` = `brand_project`.`brand_id`) and 
`user_id` = 1)'
>>> 

```

I now know that calling for a collection of brands that have projects, with the sites of said projects included, cannot be filtered further by user ID using a where clause. This is because the `user_id` column does not exist on the brands table. So, I will need to alter my code to achieve the desired collection. Thanks to the verbosity of Tinker!

I can easily recall the previous input and tweak it and try again until I get back exactly what I am looking for.

### Padding

My final most common use case is padding out my development database. Developing different actions within the app often calls for particular things to exist in the database, so I make use of model factories ran through the Tinker console to put some records in **exactly** as I need them before figuring out how to chop and change them. For example, if I need 2 users with the role of 'admin':

```sh
>>> User::factory(2)->create(['role' => 'admin'])
=> Illuminate\Database\Eloquent\Collection {#4799
     all: [
       App\Models\User {#4891
         name: "Mr. Lowell Skiles III",
         email: "schaefer.ayana@example.net",
         email_verified_at: "2023-02-08 09:52:40",
         #password: "$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi",
         #remember_token: "JqwJILGkjh",
         role: "admin",
         updated_at: "2023-02-08 09:52:40",
         created_at: "2023-02-08 09:52:40",
         id: 15,
       },
       App\Models\User {#4784
         name: "Ms. Kari Wyman",
         email: "bboyer@example.com",
         email_verified_at: "2023-02-08 09:52:40",
         #password: "$2y$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi",
         #remember_token: "mIvodB7xnA",
         role: "admin",
         updated_at: "2023-02-08 09:52:40",
         created_at: "2023-02-08 09:52:40",
         id: 16,
       },
     ],
   }
```

## Where can Tinker be used?

So far we've only looked at using this tool in development and seen that it comes in useful in a number of ways. Some of these uses can come in handy in production also. Let me explain.

Say you have a web application where users have access to shared information and private information. All the logic for showing and hiding data according to the authenticated user is in place and working fine, as well as having a role of "admin" that enables a user special privileges like being able to view all data and even some extra routes and views (i.e. and admin panel for making alterations to the data). However, there is no implementation of a way for an admin, or anyone for that matter, to toggle another user's role from non-admin to admin or back. Now the site is growing in the number of users and you need help quickly to manage some data. What can you do?

One option is to go back to the code and implement that toggle function ASAP, passing tests and QA etc. **Or** you could use Tinker to quickly assign the role to another user directly on the server, providing you have ssh access. All you do once you are in the server is `cd` into the directory where your app lives and you can `./artisan tinker` right there and use it just like we showed in earlier examples. Any changes made here will have the same effect like they would if your actual application were to make them as it **is** your application and therefore has all the database credentials and connections in place. Assigning the role to a user is as simple as running an update command using eloquent.

Of course this approach can be considered a little risky, but I just wanted to highlight the _possibilities_ available with this tool.

## What are the things they don't tell you?

Using this tool, like any other, gets better the more experience you have with it. So I want to share with you a few things that I only learnt after some time of using it so that you can have those benefits right away.

### Updating classes

If you have a Tinker session initiated, when changes are made in the codebase, they are not reflected immediately in the session. You will need to end the session, exiting  by inputting `q`, and then restarting a new session. This is because it loads everything upon booting and does not have any "hot refresh" behaviour. This is not the hugest inconvenience. You also may have noticed in earlier examples that I was calling classes directly by name without specifying their namespace. This is thanks to Tinker's aliasing.

```sh
>>> User::first()->sites()->count()
[!] Aliasing 'User' to 'App\Models\User' for this Tinker session.
```

It does this with help of `autoload.php` the same way your application does when it is running. Sometimes, however, this doesn't work right away especially if you have been making major changes to classes and namespaces. What you will need to do in order to get this automatic aliasing back is close Tinker and run:

```sh
composer dump-autoload -o
```

> the `-o` flag optimises the autoload file

After this, restart Tinker and you can call your classes by their first names, which saves a little time.

### Using Faker/Faker inside Tinker

As mentioned before, creating records on the fly is one of the most used things with Tinker. There will be times when you need to create things that may not already have a factory instance, so you can write a small factory right there. Often you will need the help of the `Faker/Faker` package, but that doesn't work right out of the box. It must be imported and assigned to a variable, which is easily done within the Tinker shell by:

```sh
>>> $faker = Faker\Factory::create();
```

Now whenever it is needed, `$faker` can be called and chained with any of its methods for creating data to fill your database.

### Time savers

The biggest time saver for me is assigning commonly used classes to variables, just like that example above with faker. I'll often assign a `$user = User::first()` if I know I will be repeatedly calling on a single user instance.

The only thing with variables is that they do not persist from session to session. If you close Tinker, you will lose all variables assigned during that session. However the history will be available between sessions, so in true "terminal user" fashion, you can find all your previously entered commands by hitting the `↑` key (...repeatedly).

## Wrap up

All in all, I think it is safe to say that if you are developing a Laravel application, you absolutely **must** have a go with Tinker. Laravel has such huge popularity mostly due to it's ease of use and streamlined features like the plethora of `./artisan` commands, and Tinker is one of them but more of an actual super power. So go, make use of it! Build amazing things at break-necking pace! It's all there for you.
