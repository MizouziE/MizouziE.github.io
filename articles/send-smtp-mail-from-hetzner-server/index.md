---
title: "Send SMTP Mail from Hetzner Server with Firewall"
url: https://www.mizouzie.dev/articles/send-smtp-mail-from-hetzner-server/
date: 2023-06-30
description: "Laravel Mail Working locally but not on server. Getting stream_socket_client timeout error. We must allow outgoing TCP Connections in Hetzner Firewall."
tags: ["PHP","web dev","documentation","Dev Ops"]
related:
  - title: "Why Do My Pull Requests All Get Rejected?"
    url: https://www.mizouzie.dev/articles/why-do-my-pull-requests-all-get-rejected/index.md
  - title: "Laravel Live London 2026 Takeaways"
    url: https://www.mizouzie.dev/articles/laravel-live-london-2026-takeaways/index.md
  - title: "How to test protected functions with PHPUnit in your Laravel app"
    url: https://www.mizouzie.dev/articles/how-to-test-protected-functions-with-phpunit/index.md
  - title: "Undefined type 'Spatie\\Permission\\Models\\' . 'Permission' | 'Role' | 'HasRoles'"
    url: https://www.mizouzie.dev/articles/undefined-type-spatie-permission/index.md
  - title: "Common Function Comparisons in PHP"
    url: https://www.mizouzie.dev/articles/common-function-comparisons-in-php/index.md
---

# Send SMTP Mail from Hetzner Server with Firewall

![stream_socket_client timeout error](https://www.mizouzie.dev/images/stream_socket_client-error.png)


## The Error - stream_socket_client unable to connect (connection timed out)

After building a cool little app using Laravel which makes use of the fantastic (and seemingly dead simple) `symfony/mailer` and you want to send a simple email via SMTP directly from the app by setting up the necessary `.env` variables you will undoubtedly test it out on a local/dev environment and will hopefully see that it works very well. Now the fun part of deploying it to a production server using Hetzner's great cloud services. Everything is set; apache/nginx, ssl, workers, the lot and the moment of truth arrives and you go through the actions to trigger an email being sent to your email address of choice.

Click send.

Wait.

Wait some more.

Refresh inbox...

Start to wonder...

Why didn't it send?

After checking you set everything correctly you finally check the logs and see something like the above image. But why?

## The Reason

It is a timeout error on a standard PHP function `stream_socket_client()` which aims to create the secure socket between your application and the mail server so that you may start to send emails via your email server's SMTP connection. It is an unusual error to encounter as it's more common to just work or return a "connection refused" error, but in our case it just times out. There is not a great deal of feedback or evidence to point to why this is happening, but long story short, our request never even makes it out of our server.

### Hetzner Firewall

If you are like me, you wanted to save some CPU %s by utilizing the great firewall offered for free as a part of your Hetzner cloud account and have set up the firewall with various rules for incoming traffic and left it blank for outgoing traffic, which, according to the display window means that there are no restrictions whatsoever. In theory, that means that all outbound requests should be 100% unrestricted.

There is something we are not seeing here though.

### Hetzner Documentation, or lack thereof 

There is apparently no (or very little) mention of the fact that Hetzner **automatically block** outbound ports **25** & **465**. [Find more details by clicking here](https://blog.hqcodeshop.fi/archives/553-Hetzner-outgoing-mail-SMTP-blocked-on-TCP25.html). 

#### TL;DR

Hetzner are savvy to scammers building spam email servers and just making people miserable by assaulting their inboxes until the IP is blocked so they do not allow any outbound traffic on these ports typically used for SMTP mail connections. Smart. But annoying they don't really tell you until you delve into the support section of their site and notice they have a dropdown option for addressing this very specific problem.

## The Solution

So once you have found the super specific technical support like the below by clicking on your account icon in the top right and selecting `Support` from the dropdown menu:

![Hetzner Technical Support - Mail not possible](https://www.mizouzie.dev/images/hetzner-support-technical-mails.png)


You will then notice that there is a small mention of how you must send a request to have the ports unblocked and that is it. That is the solution. Send them a request via this dialogue box and after a short wait you'll be able to send mail from your Laravel app hosted on a Hetzner server just like how you can in a local environment. Fantastic.
