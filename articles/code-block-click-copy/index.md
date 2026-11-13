---
title: "Code Block Click Copy"
url: https://www.mizouzie.dev/articles/code-block-click-copy/
date: 2025-12-09
description: "How to swap a Clipboard.js dependency for vanilla JavaScript"
tags: ["web dev","open source","learning","hugo","JavaScript","no build"]
related:
  - title: "My Top 3 Benefits of Open Source Contribution"
    url: https://www.mizouzie.dev/articles/benefits-of-os/index.md
  - title: "Blade Trick Randomiser"
    url: https://www.mizouzie.dev/projects/blade-trick-randomiser/index.md
  - title: "How to test protected functions with PHPUnit in your Laravel app"
    url: https://www.mizouzie.dev/articles/how-to-test-protected-functions-with-phpunit/index.md
  - title: "3 ways to read input with a Go CLI"
    url: https://www.mizouzie.dev/articles/3-ways-to-read-input-with-go-cli/index.md
  - title: "What's the Point of All the Static Analysis"
    url: https://www.mizouzie.dev/articles/whats-the-point-of-all-the-static-analysis/index.md
---

# Code Block Click Copy

I'm a back-end leaning full stack developer, so after a whole day of combing through and fixing someone else's JavaScript code all day at work, there was no better way to blow off some steam while I sat waiting for my kid to finish his Taekwondo lesson than to read through and re-write my latest addition to this site.

## Adding copy to clipboard functionality to code blocks

The latest addition I refer to was adding little "copy to clipboard" buttons in the corners of my example code blocks throughout this site. I always find them helpful when they're available on documentation or tutorial sites, so I'd spent a couple of hours last night  adding the necessary stylesheets for pretty code blocks and then going through the Hugo forums seeing how others had tackled this. I was grateful to stumble across a discussion in which someone had kindly provided a whole example repo/branch so I pulled it down and cherry-picked the parts that made it all tick, created a `package.json` file and ran `npm ci`. Boom! We had a cool little copy icon in the corner of every code block.

Excitedly I pushed all my changes, which triggered the deployment workflows, and a minute later it was live and I was going through all my old articles marvelling at the copy-pasta-ness of it all. But... the pages felt that tiny bit slower to load. They had lost that _snap_ that had been the whole reason I was so enamored with Hugo built static sites in the first place. So before going to bed, I had a closer look at the actual contents of the `package.json` and was reminded of why the JavaScript ecosystem gets so much stick all the time. No less than **four** libraries were "required" to render these tiny icons and copy stuff to the user's clipboard. That's bonkers. So I did some [over]due diligence and learned about the [Clipboard API](https://developer.mozilla.org/en-US/docs/Web/API/Clipboard_API) which had full support in most browsers since ages ago. Why wasn't I just using that?

Let's just use that!

## Using Clipboard.js in a Hugo site

I would certainly not have been able to write this all from scratch, even with a solid understanding of the API, but I didn't have to. I already had [what was shown here](https://discourse.gohugo.io/t/how-to-add-a-copy-to-clipboard-button-to-code-blocks/49633/4?u=mizouzie) as a starting point, so it was simple to break down the steps of what needed to be done. We'll break down the function that does the heavy lifting `addCopyToClipboardButtons(containerClass, buttonClass = 'copy-button')` here:

1. Create and add the button to each of the code block 'containers'

```js
  const containers = document.querySelectorAll(`.${containerClass}`);

  containers.forEach(container => {
    const button = document.createElement('button');
    button.className = buttonClass;
    button.innerHTML = icons.faCopyRegular;
    container.prepend(button);
  });
```

2. Create the clipboard object, the most of which is handled by the main dependency

```js
  const clipboard = new ClipboardJS(`.${buttonClass}`, {
    target: function(trigger) {
      return trigger.nextElementSibling;
    }
  });
```

3. Handle a successful copy operation

```js
  clipboard.on('success', (e) => {
    if (e.action === 'copy') {
      const originalIcon = e.trigger.innerHTML;
      e.trigger.innerHTML = icons.faCheck;

      setTimeout(() => {
        e.trigger.innerHTML = originalIcon;
        e.clearSelection();
      }, iconChangeTimeout);
    }

  });
```

4. Handle an error during a copy operation

```js
  clipboard.on('error', (e) => {
    console.error('ClipboardJS Error:', e.action, e.trigger);

    const originalIcon = e.trigger.innerHTML;
    e.trigger.innerHTML = icons.faBomb; // Assuming you have a cross or 'times' icon

    setTimeout(() => {
      e.trigger.innerHTML = originalIcon;
    }, iconChangeTimeout);
  });
```

It's actually quite elegant as it is, the only gripe I really had with it is that it requires loading a whole bunch of stuff that likely unused. So based on this, we can quite easily swap out the parts that matter.

## Copy to clipboard buttons in Hugo with vanilla JavaScript

Step 1 of the above breakdown is actually perfect as it is. There is nothing other than native JS and we end up with some useful bits we can come back to later.

Step 2 is where we can start to swap out the library for the native API. I had them side by side as I was working out the bugs so we'll give it a "new" name for now

```js
  const newClipboard = window.navigator.clipboard
```

Next, our main difference between the ClipboardJS implementation and the native API is that the former is very similar to jquery and offers an `on()` method to deal with either a success or failed operation, while the latter is more aligned with the `fetch()` API which has `then()` and `catch()` methods for dealing with the successfully resolved promise or the failure to do so. This means, that unlike our steps 3 and 4 above being totally separated, we can write a more "try/catch" (hooray for PHP) style chain of instructions.

```js
containers.forEach(container => {
    const button = container.querySelector(`.${buttonClass}`);
    button.addEventListener('click', function () {
      const text = this.nextElementSibling.textContent;
      newClipboard.writeText(text)
        .then(() => {
          const originalIcon = this.innerHTML;
          this.innerHTML = icons.faCheck;

          setTimeout(() => {
            this.innerHTML = originalIcon;
          }, iconChangeTimeout);
        })
        .catch((e) => {
          console.error('Error copying to clipboard:', e.action, e.trigger);

          const originalIcon = this.innerHTML;
          this.innerHTML = icons.faBomb;

          setTimeout(() => {
            this.innerHTML = originalIcon;
          }, iconChangeTimeout);
        });
    });
  });
```

This actually reads quite nicely already for describing what it is doing:

1. Grab our containers
2. For each of them;
    1. Find the button
    2. Add a click event listener in which we;
        1. Capture the next element sibling's text content (Our demonstration code)
        2. Write that text to the operating system's clipboard
        3. Then do nice things like show the user something has happened by swapping the icon to a check mark
        4. If there was an error, show a bomb icon instead and write the error to the console
        5. After a set amount of time, swap the icon back to the clipboard icon

Now that it's converted, we can see how simple this action really is. That means that there is room for improvement! Did you notice how we've got a couple of places where we're repeating actions or redeclaring the same thing? By moving one of the `const` declarations up a scope level, it becomes accessible everywhere it is required, and we also seem to be doing two _nested_ forEach loops of the same things. In the end I thought this was a nice enough level of squished to retain it's readability and still work just as we want it to.

```js
import * as icons from './icons.js'

function addCopyToClipboardButtons() {
  const iconChangeTimeout = 1300;
  const containers = document.querySelectorAll('.highlight');

  containers.forEach(container => {
    const button = document.createElement('button');
    button.className = 'copy-button';
    button.innerHTML = icons.faCopyRegular;
    container.prepend(button);
    
    button.addEventListener('click', function () {
      const originalIcon = this.innerHTML;
      const text = this.nextElementSibling.textContent;
      window.navigator.clipboard.writeText(text)
        .then(() => {
          this.innerHTML = icons.faCheck;

          setTimeout(() => {
            this.innerHTML = originalIcon;
          }, iconChangeTimeout);
        })
        .catch((e) => {
          console.error('Error copying to clipboard:', e.action, e.trigger);

          this.innerHTML = icons.faBomb;

          setTimeout(() => {
            this.innerHTML = originalIcon;
          }, iconChangeTimeout);
        });
    });
  });
}

export { addCopyToClipboardButtons }
```

It's very likely that there are further optimisations that could be made. The `setTimeout()`s could probably go on one line, for instance, but as I said, I was waiting for a Taekwondo lesson to finish, and at this point, it did, so that's as far as I went.

Once I got home I went and did my favourite thing to do and went through my files and hit **delete** on package.json, package-lock.json and all of node_modules and even got to go back into my .github/workflows/*.yaml files and remove the entire JS build step :tada:. And then I took the great feeling that gave me and came straight into a new markdown file and started writing out this here post.

Seeing how simple yet powerful modern JavaScript can be does make me appreciate this strange little language at times. And when it is put together with something as fast and elegant as a nice static website? Forget about it. So Go Hugo some sites and see what else can be wrangled!
