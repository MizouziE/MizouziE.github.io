---
title: "Why I'm glad I used QuickAdminPanel today"
url: https://www.mizouzie.dev/articles/quickadminpanel/
date: 2022-04-20
description: "My first impression when using a third party tool to speed up the development process of a Laravel web app."
tags: ["web dev","laravel","livewire","alpineJS"]
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

# Why I'm glad I used QuickAdminPanel today

![QuickAdminPanel landing page](https://www.mizouzie.dev/images/quickAdminPanel.png)


I'm familiar with Laravel and I'm fairly familiar with Livewire and AlpineJS, so by rights I should be fine building admin panels from scratch with the tools provided by this framework and packages. In theory. In reality, for me, even though I'm not a total beginner it'd take me over an hour to build it properly and then another hour to get it all working properly with model relationships, views, components and routes all doing what they're supposed to.

To achieve this I'd have to keep one eye on the documentation and one eye on the IDE and be having a very good day free from distractions, which with family in the house, is a rarity. So today I was given a go on QuickAdmin which is a tool for generating the boilerplate code for Admin panels in a Laravel project that utilises Livewire and AlpineJS. It does this from whatever you input into a number of dropdown menus and modals that make up the easy-to-use GUI and all you need do is copy and paste the files into your project.

Now, my seasoned colleague is not a fan. Mostly because they could whip the whole thing up in a short space of time using native "artisan generate" commands in the terminal. But they have 20 years in the game and I do not. This QuickAdmin tool is a leveler though! I had it all up in the browser within 20 minutes with only another 10 minutes of tweaking and customization to fit perfectly with the already existing codebase.

The argument against using such a tool is a fair one. You should know how to do it properly. I agree. Learning how to do it properly takes months, if not years because it isn't a short process and the length of time it takes to complete while referring to documentation means it is hard to see the code as single coordinated unit. Using this tool solves that! I was able to get a perspective of the process I would not have seen for another 6 months and it was like a revelation. Now I feel like I'll be able to construct an entire admin panel from the terminal in a matter of weeks rather than years.

Moral of the story is... I think beginners should be encouraged to use tools like this BECAUSE it affords them the vision they'd only acquire after a traditionally long time. I don't think I'd use it forever, but I intend on using it for now and I'll be a much higher level of developer a lot sooner for it.

[Go try it out here!](https://quickadminpanel.com/)
