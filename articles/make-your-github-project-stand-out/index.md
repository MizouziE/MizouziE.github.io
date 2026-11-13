---
title: "How to make your GitHub project pop on social media"
url: https://www.mizouzie.dev/articles/make-your-github-project-stand-out/
date: 2023-02-16
description: "Half the battle of progressing your open source project is getting it noticed and attracting contributors. So make the most of this simple trick."
tags: ["web dev","meta tag","GitHub","open source"]
related:
  - title: "What's the Point of All the Static Analysis"
    url: https://www.mizouzie.dev/articles/whats-the-point-of-all-the-static-analysis/index.md
  - title: "Why Do My Pull Requests All Get Rejected?"
    url: https://www.mizouzie.dev/articles/why-do-my-pull-requests-all-get-rejected/index.md
  - title: "Code Block Click Copy"
    url: https://www.mizouzie.dev/articles/code-block-click-copy/index.md
  - title: "My Top 3 Benefits of Open Source Contribution"
    url: https://www.mizouzie.dev/articles/benefits-of-os/index.md
  - title: "How I just customised my featured link thumbnail"
    url: https://www.mizouzie.dev/articles/customised-featured-link-thumbnail/index.md
---

# How to make your GitHub project pop on social media

![A Twitter card of a GitHub project](https://www.mizouzie.dev/images/github-project-stand-out.png)


<!-- Intro to problem -->
## The Problem

Working on an open source project is one of the best things a developer can do. The opportunities to learn and network are huge compared working on solo projects. The only way I can think of getting even more out of open source than contributing is to actually create and manage a project yourself. I tried this, early on in my career in tech and it was like strapping two great big rockets to my back. I started a project using NodeJS, which I was very inexperienced with at first, but within just a few months I had reviewed so many new things that I never would have seen if I just _studied_ JavaScript by myself.

The project still exists today, it's the news scraper API you can find [by clicking here](/projects) to go to my projects page. Before you do go and check it out, I can tell you that the thing I was most successful with was attracting new contributors. For such a basic project, we ended up having over 30 contributors. I want to share with you one of the key factors I believe helped us to reach that number.

<!-- What differnece will actually occur? -->
## The Solution

To get attention, you need to draw eyes. I'm speaking particularly about drawing eyes on social media. When you share the project, GitHub will automatically make a generic image for the various platforms that looks just that... generic. It will have the title of the project and a small version of your user profile picture, besides that are some statistics about the repo that are just too small to really see on a phone screen so are kind of a waste in my opinion.

What you have the option to do, though, is add your own image to be used in the project repository's homepage meta tags. The tags are the ones highlighted below, `og:image` & `twitter:image`:

![Screenshot of inspecting the homepage of a GitHub repo](https://www.mizouzie.dev/images/ogimage-twitterimage-meta-tags.png)


What those meta tags do is preload the image from the url in the `content` attribute for social media platforms that use open graph and for twitter. The result is the huge thumbnail, like the first screenshot at the top of this article, instead of the boring auto-generated-über-generic thumbnail.

<!-- Choose something eye-catching -->
## The Image

Being able to choose what image will appear on people's twitter feeds and facebook/LinkedIn walls put you at an advantage, so seize it by choosing an eye-catching image.

- Use bright colours
- Have short, clear text
- Tempt passers by to **click here now**
- Try to keep the aspect ratio close to 1200 pixels x 627 pixels (1.91/1 ratio)

<!-- Where to add it -->
## The How

When you have the right image you need to know where you can add it so that GitHub will store it and inject the url of the stored image into the landing pages \<head>...\</head> tags.

![GitHub repo settings](https://www.mizouzie.dev/images/github-repo-settings.png)


Click on the `Settings` tab on the menu of your repository, and then scroll down tot he `Social Preview` section. Here you click on the `Edit` button to upload your image:

![GitHub social preview section](https://www.mizouzie.dev/images/github-social-preview.png)


<!-- Share share share -->
## The Point

That's it. Done.

Now, whenever a link to your repository is shared, a nice attractive image will fill the square and help to raise your click-rate. Be proud of your project, go show it off, go grab your next contributors by the eye-balls!

**SHARE! SHARE! SHARE!**
