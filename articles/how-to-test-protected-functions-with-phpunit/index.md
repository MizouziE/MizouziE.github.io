---
title: "How to test protected functions with PHPUnit in your Laravel app"
url: https://www.mizouzie.dev/articles/how-to-test-protected-functions-with-phpunit/
date: 2023-05-13
description: "An essential part of developing an app is testing your code. My preferred method of testing my Laravel code is using PHPUnit and this is how I use reflection to test those 'harder to reach' protected and private methods inside classes."
tags: ["web dev","laravel","learning","PHP"]
related:
  - title: "Why Do My Pull Requests All Get Rejected?"
    url: https://www.mizouzie.dev/articles/why-do-my-pull-requests-all-get-rejected/index.md
  - title: "Laravel Live London 2026 Takeaways"
    url: https://www.mizouzie.dev/articles/laravel-live-london-2026-takeaways/index.md
  - title: "Undefined type 'Spatie\\Permission\\Models\\' . 'Permission' | 'Role' | 'HasRoles'"
    url: https://www.mizouzie.dev/articles/undefined-type-spatie-permission/index.md
  - title: "Common Function Comparisons in PHP"
    url: https://www.mizouzie.dev/articles/common-function-comparisons-in-php/index.md
  - title: "If it doesn't make sense, build a little version"
    url: https://www.mizouzie.dev/articles/build-a-little-version/index.md
---

# How to test protected functions with PHPUnit in your Laravel app

![reflection classes](https://www.mizouzie.dev/images/reflection.png)


## Typical places this arises

Building an application in PHP with Laravel means that we can call on a great number of classes to do various parts of the overall process we wish to achieve. For the purposes of this example (and where I first came across this issue) we will look at writing tests for a Job Class. A job class is one which will be sent some data and then process that data in the background via a worker so that the application is free to continue processing other commands.

A job class will often contain methods and variables that are;

- public
- private
- protected

As you can imagine, public methods are easy enough to test against as they are "exposed" to the outside of the class instance. It gets a little more difficult to gain access to these private and protected methods for testing, as they are intended to be called only by other methods within the class instance. They'll be things like getters and setters so that a handle() function does not get clogged up with logic for retrieving it's necessary data.

When trying to run a test against such a class with private and protected methods, we will run into errors that look like the following two examples.

## Error: Call to private Some\ExampleClass::__construct() from scope Tests\Feature\Some\ExampleClassTest

What this error message is telling us is that we are trying to invoke a __construct() method inside the `Some\ExampleClass`, which is not allowed from the scope of our test that may look something like the following:

```php
namespace Tests\Feature\Some;

class ExampleClassTest extends TestCase
{
    /** @test */
    public function my_job_does_something()
    {
        $job = new \Some\ExampleClass($input);

        // Make some assertions
        ...
    }
}
```

Just in that single line, we have already encountered the problem. If we look at the example class itself, we'll see what trips us up:

```php
<?php

declare(strict_types=1);

namespace Some;

final class ExampleClass
{
    private function __construct(
        public readonly int $input,
    ) {
    }
    ...
}
```

Our attention should be drawn to the `private function` part. That constructor called on initiation **CANNOT** be called from outside of the namespace `Some`, we are trying to call it from `Tests\Feature\Some\ExampleClassTest`.

## Error: Call to protected method Some\ExampleClass::exampleMethod() from scope Tests\Feature\Some\ExampleClassTest

This error is very similar to the above, just in this case we may have a `public function __construct()` so our class may be instantiated from outside, but our helper functions cannot be invoked outside of the `handle()` method. If we use the same example as above, we can get one step further before hitting a wall:

```php
namespace Tests\Feature\Some;

class ExampleClassTest extends TestCase
{
    /** @test */
    public function my_job_does_something()
    {
        $job = new \Some\ExampleClass($input);

        $result = $job->exampleMethod();

        // Make some assertions like...
        // $result->assertEquals('expected', $result);
        ...
    }
}
```

So the class is created with no problem, but we want to test methods that are called from _within_ the `handle()` method so we can be certain that the data being handled is the correct data.

## Solution: ReflectionClass

> Available in; PHP 5, PHP 7, PHP 8

The Reflection API in PHP is a way to retrieve any and all information from a class during runtime. The way we can use it in this case is to effectively make an instantiated "copy" of the real class we wish to test, "grab" the method we wish to test from the _reflection_ of the class, and then tweak it ever so slightly to make the private function public so that we may use it from outside of the class which in this case, is from out test.

Sounds simple enough...

### new ReflectionClass($exampleClass)

This first step is to create the reflection class on which we can make our needed modifications. There is actually a small **pre**-first step to take and that is to make a `new` instance of the class we wish to reflect and save it to a variable which we _then_ use in our call to create a `new ReflecionClass()`.

```php

        $exampleClass = new ExampleClass($input);

        $reflection = new ReflectionClass($exampleClass);
        
```

Now we have something to work with that is a little more malleable and we can decide next what we want from this reflection. Based on the previous examples of errors, let's look at getting:

- the `__construct()`
- or some named method like `exampleMethod()`

### getConstructor()

To get the constructor (public _or_ private) so that we can feed it with whatever values we need to test against, we have this handy function which may be used as such to set the `__construct()` of the class as a useable variable:

```php

        $exampleClass = new ExampleClass($input);

        $reflection = new ReflectionClass($exampleClass);
        $constructor = $reflection->getConstructor();
        
```

### getMethod()

To get the protected method that we wish to test against we have this method that can be used in a very similar way to the above:

```php

        $exampleClass = new ExampleClass($input);

        $reflection = new ReflectionClass($exampleClass);
        $method = $reflection->getMethod('exampleMethod');
        
```
### setAccessible()

With both the the `get` helpers shown, all we are doing is saving the methods **as they are** to a variable. They haven't yet been unlocked for us to use as we please, but this is where this method steps in and shows the real benefit of this whole process. As easily as this, we can change the methods accessibility so that future calls to it from our non-matching namespace do not set off any errors.

```php

        $exampleClass = new ExampleClass($input);

        $reflection = new ReflectionClass($exampleClass);
        $method = $reflection->getMethod('exampleMethod');
        $method->setAccessible(true);

```
### invokeArgs()

Now that we have a useable version of the protected/private method we wish to test the output of, we can call upon that method using this function which also feeds the method the needed parameters (arguments). Similarly to the `setAccessible()` we chain it on and it expects two arguments:

1. The object to invoke upon. (null can be used here in the case of static methods)
1. An array of arguments that the method to be invoked expects.


```php

        $exampleClass = new ExampleClass($input);

        $reflection = new ReflectionClass($exampleClass);
        $method = $reflection->getMethod('exampleMethod');
        $method->setAccessible(true);

        $result = $method->invokeArgs($exampleClass, [$arg1, $arg2, ...]);

        // Make assertions against the resulting output like...
        // $this->assertEquals('expected result', $result);

```

## Run your tests

That's it! After just a few extra lines of code, you are now able to make assertions in your PHPUnit test suites against otherwise inaccessible functions and you can sleep a little easier tonight knowing that the deepest trenches of your application are fully covered and there will not be any unwelcome bugs!

... at least not from the parts you wrote proper tests for.
