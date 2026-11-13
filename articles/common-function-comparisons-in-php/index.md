---
title: "Common Function Comparisons in PHP"
url: https://www.mizouzie.dev/articles/common-function-comparisons-in-php/
date: 2023-03-04
description: "How do you decide between two functions that, at a glance, seem to do the same thing? Let's pick apart the differences to try and make it easier."
tags: ["web dev","PHP","learning"]
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

# Common Function Comparisons in PHP

![Making up your mind which PHP function to use](https://www.mizouzie.dev/images/php-function-comparison.png)


## PHP Functions

PHP is an arsenal of tools that can process data in a multitude of ways. This is one of the reasons that it powers ~75% of website server-side code. It is fast, has huge support and scales very well.

If you were to investigate how many core functions PHP has on v8.1.2 you would find:

```sh
>>> $funcs = get_defined_functions();
=> [
     "internal" => [
       "zend_version",
       "func_num_args",
       "func_get_arg",
       "func_get_args",
       "strlen",
       "strcmp",
       "strncmp",
       "strcasecmp",
       "strncasecmp",
       "error_reporting",
       "define",
       "defined",
       "get_class",
       "get_called_class",
       "get_parent_class",
       "is_subclass_of",
       "is_a",
       "get_class_vars",
       "get_object_vars",
       "get_mangled_object_vars",
       "get_class_methods",
:

>>> echo count($funcs['internal']);
=> 1735
```

That is not a small amount by any means, so it is impossible to know them all. Even the most experienced PHP developers will regularly refer to [php.net](https://www.php.net/) to look up which they can use for what.

With 1735 functions being available, surely some must overlap or be similar. In this discussion we will look at the ones that seem like they could be the same thing and see if we can decide best use cases for each.

### Most commonly used

Looking through [Exakat](https://www.exakat.io/en/the-100-php-functions-in-2022/)'s _top 100 php functions 2022_, we can see a few likely candidates to choose from. Lets take:

- [implode()](https://www.php.net/manual/en/function.implode)
- [count()](https://www.php.net/manual/en/function.count)
- [explode()](https://www.php.net/manual/en/function.explode)
- [trim()](https://www.php.net/manual/en/function.trim)
- [strtr()](https://www.php.net/manual/en/function.strtr)

> <sup>click on any to see the official definitions<sup>

## Compared to their closest cousins

### implode() vs join()

Join is actually just an alias, so they're exactly the same. Implode works like running:

```php
function implode(string $separator, array $arr): string
{
    $imploded = '';

    foreach ($arr as $index => $str) {

        switch ($index) {
            case !0:
                $imploded .= $separator;
            default:
                $imploded .= $str;
                break;
        }
    }

    return $imploded;
}
```

Going through the steps above, it does something along the lines of:

1. Initiate an empty string variable as `$imploded`
1. Loop through the input array
1. If the element of the array is at index 0, **do not** concatenate the separator to `$imploded`
1. Otherwise concatenate given separator string
1. Concatenate element to `$imploded`
1. Repeat until all elements of array have been cycled
1. Return fully constructed string `$imploded`

### count() vs if (!count()) {}

This function has such high popularity due to it being used commonly for frontend "decision making". When choosing whether or not to display a particular component depending on there being any instances of something in the backend database, developers will call count() on a parameter passed to the view being displayed. The idea is that if it returns a "truthy" value, as in **not** 0, then it confirms the existence of at least one instance that parameter in the database.

This can be fine for a single call and if there is not an enormous number of instances of that parameter. Often though, there will be a large number of conditional components which means multiple calls to count() which ends up counting an enormous amount of your database. This is not ideal for UX as it can make the page load excruciatingly slowly. There is a simple optimization demonstrated below:


```html
// Rather than calling count() on everything like so...
<?php if ($param->count()) { ?>
  <div>Show this example HTML component!</div>
<?php } else { ?>
  <div>Show alternate HTML or Nothing 🤷</div>
<?php } ?>

// ...we can check NOT == 0 like this instead
<?php if (!$param->count()) { ?>
  <div>Show alternate HTML or Nothing 🤷</div>
<?php } else { ?>
  <div>Show this example HTML component!</div>
<?php } ?>

```

The optimized second option flips the original and checks only for a **non-falsey** value which is satisfied by the very first counter beyond 0. After that first 1 is counted, the rest of the code can move on and display the non-existent case or the existent case accordingly. We are effectively only "counting" a maximum of the number of calls to count as opposed to counting the number of calls to count **multiplied** by the number of instances of the counted records in the database. That time can add up! Count one or zero and move on.

### explode() vs str_split() vs str_tok()

These functions are all about creating an iterable out of a given string, each splitting the string at a chosen point. The way that point is chosen differs depending on which function is called.

- `explode()` searched through the string for a given sub-string and splits around that
- `str_split()` simply counts out the specified number of bytes (i.e. characters in the ASCII set) and divides the string there, then starts counting again from the split to find the next split location.
- `str_tok()` the same as explode() but does not return the whole split up string at once.

The first two will return an array of the "pieces" of strings they have cut. This is handy when you need to derive a string from a url string, for example you can call:

```php
>>> $string = "https://mizouzie.dev/articles/that/help/developers/learn";
=> "https://mizouzie.dev/articles/that/help/developers/learn"

>>> explode('/', $string);
=> [
     "https:",
     "",
     "mizouzie.dev",
     "articles",
     "that",
     "help",
     "developers",
     "learn",
   ]
```

While with str_split() you will give an integer value (n) and it splits the string after each nth character.

The function str_tok() is the outlier here as it only returns the first section (or token, as the name suggests) on it's first call. The interesting thing is that it will return the **next** section on the next call, and the next one only on the next call after that and so on until it has none left to return and returns `false`. This makes it ideal for use within a loop, as the returning of a falsey value will break out of the loop. It just removes the step of _creating_ the iterable array to _then_ do something with it. You can just get right down to business with this one.

### trim() vs ltrim() vs rtrim() vs preg_replace()

Trimming strings is a common necessity as many strings have invisible and undesirable baggage in the form of whitespace either lurking before or behind them. A string ending in a _new line_ or _carriage return_ character can wreak havoc on simple processing functions or scripts, so it is always wise to sanitize them with some sort of trim at the very least.

- `trim()` indiscriminately removes whitespace from in front and behind a string
- `ltrim()` & `rtirm()` remove whitespace from the left and right of a string respectively
- `preg_replace()` requires a little knowhow of the dark-art of regular expressions

Should you be familiar or at least comfortable with regex, you may specify exactly what type of whitespace is removed, where it is removed from or even only remove it given certain conditions. It is like the super-max-pro Premium fully licensed version of the trimming functions before it, as well as much more.

### strtr() vs str_replace()

For me, these two do the same thing. Search a string for a given substring, and then return that string with the substring replaced with a given alternative substring. Both accept arguments as strings or arrays, but there is a slight difference in _how_ they accept the arrays.

The arrays passed to str_replace() (notice the plural **arrays**) will be one array of search values and a second array of the search value's replacements. The search values and replacements will need to match up according to index in order to be executed properly.

The **single** array passed to strtr() feels like a shortcut as the index _is_ the search value and the value is the replacement. i.e. `['from this' => 'to that']`.

The usefulness of this is determined by how you go about construction your arrays to be passed. In many cases it may be easier or faster to construct a single array with string indexes. For me, that option just feels more sensible, less room for error.

## Summary

Now that we have looked at some of the most common similarities and differences, I hope you will consider yourself well armed and well informed on the great choice of incredibly powerful weapons at your disposal!

_Now go slay some data._

![choose your weapon](https://www.mizouzie.dev/images/choose-your-weapon.png)
