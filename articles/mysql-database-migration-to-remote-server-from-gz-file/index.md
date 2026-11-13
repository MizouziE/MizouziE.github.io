---
title: "MySQL Database Migration to a Remote Server from a .gz mysqldump file"
url: https://www.mizouzie.dev/articles/mysql-database-migration-to-remote-server-from-gz-file/
date: 2023-07-01
description: "Some bugs only appear when your web application is truly tested out in production. The actual use cases of your data structure may differ from what you imagined, so make a dump of the prod DB and play around on a development server."
tags: ["mysql","tutorial","CLI","learning","linux"]
related:
  - title: "3 ways to read input with a Go CLI"
    url: https://www.mizouzie.dev/articles/3-ways-to-read-input-with-go-cli/index.md
  - title: "Code Block Click Copy"
    url: https://www.mizouzie.dev/articles/code-block-click-copy/index.md
  - title: "How to test protected functions with PHPUnit in your Laravel app"
    url: https://www.mizouzie.dev/articles/how-to-test-protected-functions-with-phpunit/index.md
  - title: "Tum | Modo | Postea"
    url: https://www.mizouzie.dev/articles/then-now-after/index.md
  - title: "Space is the Place: Reasons to explore Twitter Spaces"
    url: https://www.mizouzie.dev/articles/space-is-the-place/index.md
---

# MySQL Database Migration to a Remote Server from a .gz mysqldump file

![Title image](https://www.mizouzie.dev/images/mysqldump-file-to-remote-server.png)


## Introduction

I had a task a few months back of discovering the cause of a bug in a production environment that just was not presenting itself in development, mostly due to the creators and users of the application having ever so slightly differing ideas about how the app should be used. This meant that in all of our testing while making the application, we never saw the bug that was being reported. The solution for this was to take a copy of the production database and use that in development to try and replicate the reported issue.

The application was a CMS (Content Management System) that had a few extra niceties that introduced a little more complex database structuring, so that could be one reason it was hard for the development team to preempt this problem. All the same, the tech lead went ahead and made a mysqldump file `blogs.sql.gz` and promptly forwarded it to me to get to work with figuring things out. All our development work is done on a dedicated development remote server, and I had the file on my local machine so I'd need to extract and upload the file to the remote server, which was something  I had not previously done.

I will cover the steps I went through in order to discover how exactly how to get the database created and populated with the desired data. It was a journey of discover for me as I was making use of tools that I was familiar with as well as new (to me) tools and therefore had some steps that may not be deemed _necessary_, but I will include them as they taught me something and my goal is to share those lessons.

I had a few specific problems which I will explain in more detail further down, which caused a few of the extra steps I took so there is more to learn here than just uploading data. As well as just documenting my thought process for my own benefit, I hope to offer some guidance to other junior developers in ways to approach the type of problems that cost us a lot of time when we are starting out.

### TL;DR

I went through steps that allowed me to read the dump file, attempted to pipe that to the remote server, read the error messages and made whatever changes I needed and repeated until I had what I needed and eventually uploaded the data so I could have an exact copy of production on my development environment.

Skip down to [Final working Command](#final-working-command) to see the command I ended up with.

### Use case

In the case I am presenting here we have;

- a zipped mysqldump file (.gz) located in `./Downloads/blogs.sql.gz`
- the file contains a number of `mysqldump: [Warning]/Error...` lines (due to being made with/without a password)
- a remote server to which we have ssh access, denoted by `<user>@<hostname>`
- mysql installed on said server

#### Particular problems we face

The first stumbling block was the warnings that do not parse normally when uploading a mysqldump file. Their presence breaks the stream and so nothing after them will be read. I ended up solving this problem in more than one way as you will see by the end. My first "solution" introduced new problems that became apparent later on.

#### Other uses

There is no reason I see that the working knowledge highlighted in the rest of this article cannot be beneficial to other use cases where, for example, a mysql dump file devoid of any warnings needs to be uploaded. I will break down each part of any commands we examine, so you can decide which you need to apply for your case.

### Subjects we will cover

A number of tools will be covered, so I will introduce them briefly here and explain their specific uses and additional flags when appropriate.

| Command | Description                                       |
|---------|--------------------------------------------------:|
| gzip    | compress or expand files                          |
| tail    | output the last part of files                     |
| head    | output the first part of files                    |
| ssh     | OpenSSH remote login client                       |
| mysql   | a simple SQL shell that supports interactive use  |
| sed     | stream editor for filtering and transforming text |
| \| (pipe)| help combine two or more commands and are used as input/output concepts in a command.  |

## Steps

You can see the whole raw interaction I had by clicking [here](/../../mysqldump-steps) and see the MySQL set up by clicking [here](/../../mysql-setup).

### 1. Create the database

The first step is to use `ssh` to access the development server and be able to make changes using the `mysql` shell to add a new database that we will populate from the dump file.

```sh
sam@Mizouzie:~$ ssh <user>@<hostname> -A
```

This will get us in.

```sh
<user>@<hostname>:$ mysql
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 15401
Server version: 10.6.12-MariaDB-1:10.6.12+maria~deb11 mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```

Now on the development server, use the `mysql` command to open the shell client.

```sh
MariaDB [(none)]> show databases;
+-----------------------+
| Database              |
+-----------------------+
| information_schema    |
| content_generator     |
| example_laravel       |
| next_con_gen          |
+-----------------------+
4 rows in set (0.001 sec)

MariaDB [(none)]> create database example_laravel_dummy
    -> ;
Query OK, 1 row affected (0.000 sec)

MariaDB [(none)]> show databases;
+--------------------------+
| Database                 |
+--------------------------+
| information_schema       |
| content_generator        |
| example_laravel          |
| example_laravel_dummy    |
| next_con_gen             |
+--------------------------+
5 rows in set (0.001 sec)
```

Use `show databases;` to check existing databases In this case our development DB is **example_laravel** so we will create a dummy version **example_laravel_dummy** which we'll use later. Use `show databases;` once more to confirm it was created.

### 2. Try to import directly

```sh
sam@MizouziE:~$ ssh <user>@<hostname> -A "mysql -u sam -p example_laravel_dummy" < ./Downloads/blogs.sql.gz 
Enter password: ERROR 1045 (28000): Access denied for user 'sam'@'localhost' (using password: YES)
```

Here we are using `ssh` and feeding a command into it by passing a string between quotation marks. Effectively we are running `mysql -u sam -p example_laravel_dummy` **on** the remote server but **from** our local machine.

I should not have used the `-p` flag for a password, so let's omit that.

```sh
sam@MizouziE:~$ ssh <user>@<hostname> -A "mysql -u sam example_laravel_dummy" < ./Downloads/blogs.sql.gz 
ERROR: ASCII '\0' appeared in the statement, but this is not allowed unless option --binary-mode is enabled and mysql is run in non-interactive mode. Set --binary-mode to 1 if ASCII '\0' is expected. Query: Sd'.
```

Now the file format is a problem as we have not extracted it.

```sh
sam@MizouziE:~$ ssh <user>@<hostname> -A "mysql -u sam example_laravel_dummy" < gzip -dk ./Downloads/blogs.sql.gz 
bash: gzip: No such file or directory
```

We need to install `gzip`, so go ahead and do that.

```sh
sam@MizouziE:~$ gzip -cd ./Downloads/blogs.sql.gz | ssh <user>@<hostname> -A "mysql example_laravel_dummy"
ERROR 1064 (42000) at line 1: You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near 'mysqldump: [Warning] Using a password on the command line interface can be in...' at line 1
```

The two flags used `-c` & `-d` with `gzip` will write the output on to standard output and decompress which is what allows us the then pipe that output into our following `ssh` command.

Also note that we have inverted the command to run `gzip` first so that we pipe the output of that into the `ssh` command.

Our new error indicates a syntax error in the SQL statement we wish to run via the `mysql` command. That brings us to the next step.

### 3. Read the mysqldump file

In order to see why and where we have this issue, we can use the `head` command to read the first 10 lines of the file after unzipping it.

```sh
sam@MizouziE:~$ gzip -cd ./Downloads/blogs.sql.gz | head
mysqldump: [Warning] Using a password on the command line interface can be insecure.
-- MySQL dump 10.13  Distrib 8.0.33, for Linux (aarch64)
--
-- Host: localhost    Database: blogs
-- ------------------------------------------------------
-- Server version	8.0.33

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
```

We can see by this output that the line beginning with `mysqldump: [Warning]` is problematic and not recognised as proper SQL syntax. Furthermore, the following lines are commented out so they could also be omitted.

### 4. Skip first 7 lines

So as our first 7 lines contain bad syntax and useless information, let's send the same output but without the first 7 lines. Similar to `head`, we can use `tail` to send everything after a specified line number by making use of the `-n` flag with a positive (+) integer.

```sh
sam@MizouziE:~$ gzip -cd ./Downloads/blogs.sql.gz | tail -n +7 | ssh <user>@<hostname> -A "mysql example_laravel_dummy"
ERROR 1064 (42000) at line 12: You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near 'mysqldump: Error: 'Access denied; you need (at least one of) the PROCESS priv...' at line 1
```

New error, but similar to the one we came across before. This means we must delve a little deeper down the file.

### 5. Read further down the file

We can use a `-n` flag on head to specify the number of lines shown. Without this flag it defaults to 10, so we will use a higher number to see more than before.

```sh
sam@MizouziE:~$ gzip -cd ./Downloads/blogs.sql.gz | head -n 25
mysqldump: [Warning] Using a password on the command line interface can be insecure.
-- MySQL dump 10.13  Distrib 8.0.33, for Linux (aarch64)
--
-- Host: localhost    Database: blogs
-- ------------------------------------------------------
-- Server version	8.0.33

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!50503 SET NAMES UTF8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;
mysqldump: Error: 'Access denied; you need (at least one of) the PROCESS privilege(s) for this operation' when trying to dump tablespaces

--
-- Table structure for table `activity_log`
--

DROP TABLE IF EXISTS `activity_log`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
```

In this output we see that there is another error message that breaks syntax rules. This one follows after a number of settings that may be safe to assume are ok by default, so we will try to use `tail` like before but from ven further down the file.

```sh
sam@MizouziE:~$ gzip -cd ./Downloads/blogs.sql.gz | tail -n +24 | ssh <user>@<hostname> -A "mysql example_laravel_dummy"
ERROR 1005 (HY000) at line 96: Can't create table `example_laravel_dummy`.`article_background_image` (errno: 150 "Foreign key constraint is incorrectly formed")
```

Now we see that this will not work because we omitted something important. Namely the line `/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;`.

### 6. Set important server variables

We can try to set the missing server variable and keep our current way of feeding the SQL statement by using `--init command=[command]` after `mysql`. We will use that to disable the foreign key checks.

```sh
sam@MizouziE:~$ gzip -cd ./Downloads/blogs.sql.gz | tail -n +24 | ssh <user>@<hostname> -A "mysql --init-command=\"SET SESSION FOREIGN_KEY_CHECKS=0;\" example_laravel_dummy"
ERROR 1231 (42000) at line 2261: Variable 'time_zone' can't be set to the value of 'NULL'
```

Here we have yet a different error, but it's cause is very similar to the last error we encountered. It is down to omitting those seemingly "safe to omit" server variables from the start of the mysqldump file. We need another approach.

### 7. That's what she `sed`

`sed` now makes itself useful to us as we can use it with it's regexp matching ability to _remove_ certain lines from the file stream as they are being streamed. What I mean is, we will just drop out the lines starting with the `mysqldump: ` that are causing us headache.

We achieve this by putting 'mysqldump:' in a regexp that deletes any line starting with it as follows; `'/mysqldump:/d'`. It's the lowercase 'd' that does the business.

```sh
sam@MizouziE:~$ gzip -cd ./Downloads/blogs.sql.gz | sed '/mysqldump:/d' | ssh <user>@<hostname> -A "mysql --init-command=\"SET SESSION FOREIGN_KEY_CHECKS=0;\" example_laravel_dummy"
sam@MizouziE:~$
```

And look at that, **no error**! That means it worked.

> There is one thing not entirely necessary here, though. If you spot it shoot me a DM on twitter [@mizouzie](https://twitter.com/mizouzie)

### 8. Final check to make sure

Now all that is left to do is check on our mysql instance on the remote server if everything is as we expect.

```sh

MariaDB [example_laravel_dummy]> show tables;
+------------------------------------+
| Tables_in_example_laravel_dummy |
+------------------------------------+
| activity_log                       |
| article_article                    |
| article_author                     |
| article_background_image           |
| article_category                   |
| article_revisions                  |
| article_site                       |
| article_slugs                      |
| articles                           |
| author_revisions                   |
| author_slugs                       |
| authors                            |
| background_images                  |
| background_images_revisions        |
| blocks                             |
| categories                         |
| category_revisions                 |
| category_site                      |
| category_slugs                     |
| failed_jobs                        |
| features                           |
| fileables                          |
| files                              |
| imports                            |
| linked_images                      |
| links                              |
| mediables                          |
| medias                             |
| menu_revisions                     |
| menus                              |
| migrations                         |
| password_resets                    |
| provider_revisions                 |
| provider_slugs                     |
| providers                          |
| ratings                            |
| redirects                          |
| related                            |
| setting_translations               |
| settings                           |
| site_revisions                     |
| site_user                          |
| sites                              |
| slider_revisions                   |
| sliders                            |
| string_translation_revisions       |
| string_translations                |
| tagged                             |
| tags                               |
| twill_password_resets              |
| twill_users                        |
| users                              |
+------------------------------------+
52 rows in set (0.001 sec)

MariaDB [example_laravel_dummy]> ^DBye
```

Nice, we have all our tables and then we exit with `Ctrl + D`.

## Final working command

After all our experimenting we got what we wanted, so our final command to do what we want looks like this:

```sh
sam@MizouziE:~$ gzip -cd ./Downloads/blogs.sql.gz | sed '/mysqldump:/d' | ssh <user>@<hostname> -A "mysql example_laravel_dummy"
```

So for any other use case, or just a a breakdown of each part, we had something like this:

```sh
<user>@<localhost>:~$ gzip -cd <location/of/mysqldump.sql.gz> | sed '/<beginning of line to remove>/d' | ssh <user>@<hostname> -A "mysql <name_of_database>"
```

So the command does this:
1. Unzip the file
1. Pipe the stream of the file output through `sed`
1. Filter out undesired lines with `sed`
1. Pipe the result of that to `ssh`
1. With `ssh` execute the `mysql` command and feed the piped stream to a named database

This all works because the zipped mysqldump file is essentially one big SQL statement that can recreate all tables and insert all data that was in the database it was taken from. They tend to be quite large files, hence the need to zip them.

## Summary

This was a series of steps that ended up giving the desired results. There were probably a number of different ways to arrive at teh same conclusion, or even ways to arrive at a different conclusion that gave the same result. That is the cool thing about working in this field, we have a multitude of tools that can do small parts of a solution to a problem. It is up to us to do exercises like this to learn how to combine them into something that gives a result. That is what we software engineers get paid for. Practice problems like this, you'll learn loads!
