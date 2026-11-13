---
title: "Why use Docker for databases and how"
url: https://www.mizouzie.dev/articles/why-use-docker-for-databases-and-how/
date: 2023-02-05
description: "Docker is an invaluable piece to add to any tech stack for a multitude of reasons, but it can often be confusing to work with. Here we'll dive into my favourite way to use Docker containers and explain every step   ."
tags: ["web dev","docker","learning"]
related:
  - title: "Code Block Click Copy"
    url: https://www.mizouzie.dev/articles/code-block-click-copy/index.md
  - title: "Tum | Modo | Postea"
    url: https://www.mizouzie.dev/articles/then-now-after/index.md
  - title: "How to test protected functions with PHPUnit in your Laravel app"
    url: https://www.mizouzie.dev/articles/how-to-test-protected-functions-with-phpunit/index.md
  - title: "Space is the Place: Reasons to explore Twitter Spaces"
    url: https://www.mizouzie.dev/articles/space-is-the-place/index.md
  - title: "3 things the best senior developers do"
    url: https://www.mizouzie.dev/articles/best-senior-developer/index.md
---

# Why use Docker for databases and how

![Moby the Docker whale and all his friends](https://www.mizouzie.dev/images/Moby-Group-Docker.jpeg)


Sections

## What is Docker 

Docker is a tool used to create what are known as containers, yes you can imagine it like a shipping container on a cargo ship (hence the company's logo/mascot, Moby). The containers themselves are like small virtual machines that run a specific package of software that is defined by something called an image. The image tells Docker exactly what the small virtual machine needs to be and do and Docker creates a software only version of exactly that which runs _on_ your host machine **but** still _completely_ separate from your machine. You could consider it a separate node on a network that only the host machine _can_ have access to. I say _can_ have access because if you want to be able to communicate with the container, you will need to have a network set up, but we will look at this later.

## What database can be used?

For the sake of this article, we will use MariaDB as our database example, but you could apply most of the theory we'll cover to any database of your choosing. I am a back-end-leaning-full-stack kind of developer, so my personal favourite is relational SQL over NoSQL, and although MySQL is the industry standard, I personally prefer to use MariaDB whenever possible because it's open source and I'll choose open source over corporations any day.

Whatever your choice of database, there will be an image for you to pull and work from where the set up steps will;

1. be very similar to what we will go over here
1. any differences will be apparent when you read the necessary docs, because we all RTFM, right?...

## How does a MariaDB container work?

Like we saw before, the MariaDB container will just be like a stand-alone computer on the network that runs the version of MariaDB that you specify when you provide Docker with the image that you desire. If you [take a look at the official documentation for tags](https://hub.docker.com/_/mariadb/tags), you will see pages and pages of different versions that are available to suit any need imaginable.

The official docs also give nice and clear instructions on setting up a basic instance, but I remember that the very first time I read them and followed them, I did not exactly know what I was doing or why it worked. I aim to make all that clear below!

## How to set it up from the terminal

Firstly, we are going to skip over the installation process for docker, which [you can find help with here](https://docs.docker.com/get-docker/) if you have not already done that.

Now we will look at the basic command provided by the documentation and break down what each part is doing.

```sh
docker run --detach --name mariadb-container --env MARIADB_USER=mizouzie --env MARIADB_PASSWORD=mizouzie_loves_mariadb --env MARIADB_ROOT_PASSWORD=root -p 3306 -v mariadb-volume mariadb:latest
```

| command / option| explanation |
|----------------|-------------|
| docker         |tells docker that we are talking to it, like shouting 'Hey!'|
| run            |we want what follows to be run BY docker|
| \-\-detach       |flag to say 'please carry this out in the background and don't occupy the terminal' (the short version is `-d`)|
| \-\-name         |the string following this flag will be assigned as the container name|
| \-\-env          |the string following should be added as an environment variable, handy for passwords etc (the short version is `-e`)|
| -p |specify which port(s) to expose to the host machine|
| -v |create and specify the name of the volume for this container|
| mariadb:latest |the name and version of the image you wish docker to pull|

### Some things to note

After running this command with all you own details, docker will first check your local images for the one you have specified. If it doesn't find it already downloaded, it will then `pull` the image from [docker hub](https://hub.docker.com/) and use that to build the container with any and all of your given arguments like the environment variables and name.

The container will now be running and will not stop until you tell it to, which can be done by:

```sh
docker stop <the-name-you-gave-the-container>
```

The container will be accessible by a default network known as a bridge. You can read details of the network and other useful information about the container by running:

```sh
docker inspect <the-name-you-gave-the-container>
```

The huge display of information after running this command can seem daunting, but just make the terminal full screen and go through the layers one by one and you will soon start to understand the way these containers work. You will spot the environment variables you passed to it earlier, as well as all imaginable configuration key:value pairs which will mostly be set to whatever their default is, but all that is customizable if you wish to delve into the documentation.

If you just want to know what port you can access the container on you can use `grep`, for example:

```sh
docker inspect <the-name-you-gave-the-container> | grep HostPort
```

The `-v` flag to create a volume is an essential step if you want to be able to save data between stopping and starting the container. Omitting this means that on every stop/start, any data that was inserted into the database previously will be gone. Seeing as the point of a database is to _store_ data, it's a good idea not to miss this out.

### Other services that work well with it

It's all good and well having our database instance running, but while we are developing applications we often need to be able to peer inside or even feed in some raw SQL commands.

The official image docs show how to connect a MySQL command line client, so again we'll break down what means what.

```sh
docker run -it --network some-network --rm mariadb mariadb -hmariadb-container -umizouzie -p
```

| command / option| explanation |
|----------------|-------------|
| docker         |tells docker that we are talking to it, like shouting 'Hey!'|
| run            |we want what follows to be run BY docker|
| -it       |tells docker to keep an interactive terminal open to allow us to actually use the connection|
| \-\-network         |the name of the network the desired container is on, therefore we wish to join (don't worry, we will talk about these below)|
| \-\-rm          |remove this container once it is closed|
| mariadb mariadb |the first is the image we're using, the second is the command to run this container against the running container and connect using the following arguments|
| -h |the name of the host container **must match what you put earlier**|
| -u |the name of the user we want to connect as, equal to the MARIADB_USER environment variable **must match what you put earlier**|
| -p |upon creating the container, prompt me for the password which will be equal to the MARIADB_PASSWORD environment variable|

> Notice that the `-u`, `-h` & `-p` flags come after specifying the container? That is because they are "arguments" for the container itself rather than "options" for the command. Don't mix up the `-p` with the port exposing option from earlier! 
>
>They also have a slightly different syntax in which there is no space between the flag and it's value.

This will open up MySQL client right there in the terminal and you can interact with your database.

However... I find this a little cumbersome for smooth development, so I much prefer to use Adminer, which as you may have guessed by now, can be spun up in it's very own container. The setup is similar to how we set up the MariaDB container and you can [check out the official docs](https://hub.docker.com/_/adminer) for the details should you want to set it up in the terminal too. The only problem with doing this is that you must set up a named network and connect both the MariaDB container and the Adminer container to it so that they can communicate. I did it a few times just to see how it was, and it's a lot of work so I'll just briefly explain the part of creating a network, because you will see later that there is a much easier way to achieve the same results even if doing it all manually is far more educational.

### Networking containers

Keeping things separate is kind of the essence of docker containers, but they're not much use if they can't communicate with one another. This is nice and straightforward to achieve by networking.

Upon creating the MariaDB container above, it automatically made a bridge network with the host machine to expose the ports found by searching through the output of `docker inspect`, which is typically 3306, but was 5000 in our example. It gets a little out of that scope when we want to connect another service to both the MariaDB container and our host machine. This is where we must create a network which will connect all three.

Networking in docker treats the network itself as a container that you can add other containers to. The only difference is that it is noticeably more simple to set up. Just the following command, and we're good to go:

```sh
docker network create <name-of-your-network>
```

Now that the network exists, it gives us the option to connect a container to it during the `docker run` command by using the `--network` flag and naming this newly created network, or it is possible to add a running container by using:

```sh
docker network connect <name-of-your-network> <name-of-your-container>
```

Once the database is added to the network, you can add whatever method of interacting with it you choose using the same method.

## How to set it all up with docker compose

There is a much more simple way to do all of what we have discussed in one go. Creating a file named `docker-compose.yaml` inside our working directory. This is like an instruction script for automating all the terminal commands we just painstakingly typed out. Now that we know the ins and outs of all the commands, we should be able to read through the file and know what is doing what. Here is an example:

```yaml
version: "3"

services:

  mariadb:
    image: mariadb:10.7
    environment:
      - MARIADB_ROOT_PASSWORD=root
      - MARIADB_DATABASE=example_database
      - MARIADB_USER=mizouzie
      - MARIADB_PASSWORD=mizouzie_loves_mariadb
      - MARIADB_AUTO_UPGRADE=true
    volumes:
      - mariadb-volume:/var/lib/mysql
    ports:
      - "3306:3306"

  adminer:
    image: adminer:latest
    environment:
      - ADMINER_DEFAULT_SERVER=mariadb
    ports:
      - "8080:8080"

volumes:
  mariadb-volume:
```

Once we have this file present in our working directory, it can be called using a built in feature of docker called `compose`. This feature used to be a separate thing to docker, but it has been fully integrated into the docker CLI and this makes things wonderfully easy for us. A simple command of,

```
docker compose up -d
```

run from the terminal inside our working directory tells docker to look for a file named `docker-compose.yaml`, read it's instructions, and spin up the containers with all the options included within the file. If you noticed the `-d` option at the end there, that is the same `--detach` flag we used earlier that tells docker to run it in the background and free up the terminal.

Additionally to setting up the containers from the file, docker will do a couple of things automatically;

1. Create a network container for the listed containers so that they may all communicate immediately.
1. Prepend the name of the working directory to the container (and network) names

This are handy because not only does it save you the trouble of setting up networks and making connections manually, but you can easily copy + paste the same yaml file between projects and use it again without worrying that your containers will overwrite one another.

### Stopping and starting containers

Whether we start containers via the command line or a yaml file, once they are running it is easy to stop and start them as we please. We only need to make use of 3 simple commands to get the needed information and give the desired instructions. The below example shows 2 of these commands and the expected outputs:

```linux
sam@MizouziE:~/code$ docker run --detach --name mariadb-container --env MARIADB_USER=mizouzie --env MARIADB_PASSWORD=mizouzie_loves_mariadb --env MARIADB_ROOT_PASSWORD=root -p 5000 mariadb:10.7
36cef2546991c8c21e2011d0cc678026fc7258f4f922fea9d4aabfa0d4611815
sam@MizouziE:~/code$ docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                                                   NAMES
36cef2546991   mariadb:10.7   "docker-entrypoint.s…"   4 seconds ago   Up 3 seconds   3306/tcp, 0.0.0.0:32768->5000/tcp, :::32768->5000/tcp   mariadb-container
sam@MizouziE:~/code$ docker stop mariadb-container 
mariadb-container
```

- First we create a container
    - The output is the long ID of the created container
- Use `docker ps`
    - The output a list of any running containers with the most important details
- Use `docker stop <container-name-OR-container-ID>` to stop that container
    - The output is the name of the stopped container

After stopping the container, run `docker ps` again and we'll see that the specified container is no longer present in the list, but the container and all of it's data does still exist and can be restarted whenever desired. To restart a created container:

```sh
docker start <container-name-OR-container-ID>
```

Even if we forget the name or ID of a container we closed a while back, so long as we have not removed or pruned it from our system, we can call `docker container ls -a` to see a list of every container we have whether they are running or stopped.

## How I keep it organised

One of the gripes that a lot of people have with docker is managing it. The levels of complexity introduced by the lashings of automation can make your machine choke when things get cluttered, but with a little further understanding it can be managed and painless. [This tweet represents my approach](https://twitter.com/mizouzie/status/1600991700830552064?s=20&t=Etdxf8rKEbmBgvrZMW_W7w).

### Boilerplate docker-compose.yaml

Opt for the DRY approach and write yourself a reusable yaml file. As stated before, cross-contamination is avoided automatically with the naming conventions, so using the same boilerplate over and over is no problem. It makes it very easy for you to alter small details like the version between projects which is probably the main advantage of using docker for databases in the first place.

This also makes it easy to micro-manage things like the storage should you wish to override docker's automatic volumes allocation.

### Storage management

I once ran into issues with docker filling up my hard drive partition because I always left it to automatically use "volumes". These are great for ease of use, but after running multiple containers with large volumes, it can take up too much space. The problem comes from docker persisting data inside it's self-managed volumes which are associated with a container and sometimes removing a container does not remove the volume also, so it just sits there rent-free. To remove these freeloaders use:

```sh
docker volume prune
```
With that, any volume that exists but is not associated with a container will be destroyed. Bear in mind that you will loose any data if you are in between removing and rebuilding a certain container that you wish to reuse the old volume with.

Another way to avoid volume clutter is to tell docker to use bind-mount instead of automatic volumes. This can be done through the command line or more easily in the yaml file. Here is the same example from before, but with a customised bind mount path:
```yaml
version: "3"

services:

  mariadb:
    image: mariadb:10.7
    environment:
      - MARIADB_ROOT_PASSWORD=root
      - MARIADB_DATABASE=developer
      - MARIADB_USER=developer
      - MARIADB_PASSWORD=developer
      - MARIADB_AUTO_UPGRADE=true
    volumes:
      - type: bind
        source: ~/../../usr/epn-api-db
        target: /var/lib/mysql
    ports:
      - "${FORWARD_DB_PORT:-3306}:3306"

  adminer:
    image: adminer:latest
    environment:
      - ADMINER_DEFAULT_SERVER=mariadb
    ports:
      - "8080:8080"

volumes:
  mysql:
```

Under volumes we can specify the type and give a source and target as arguments. What this does is makes a tunnel from the container (path inside container = target) to the host machine (relative path on host = source). This approach effectively renders the container as only a software layer and uses the storage on your machine the same way the software would if it were running on your machine.

> **Some things to consider using this approach**
>
> - There is an issue of permissions when using this that will need to be set up for it to work
> - Changing/deleting/corrupting this data on the host will be reflected in the container

I do use this approach because I like to have full control, but it is advisable and more convenient to leave docker to manage it automatically. Just be sure to do housekeeping once in a while and clear out the dangling volumes.

## Benefits over local installation of MariaDB

Although it feels at first as a lot to learn, what it boils down to is having yourself a simple setup that can be used time and time again. This is absolutely ideal when you work with multiple projects that use different versions or even different database management systems altogether because it's only required to modify a line or two of a template file and you can have the exact needed version of the exact needed system in seconds without ever installing and configuring on your machine.

Also as a lot of applications are deployed to production using docker, so having a local development repository with as close to production environment variables is always a plus.

I am not against having a DBMS installed locally at all, but having one also means that it needs to be managed and upgraded by you, whereas using prefabricated recipes from the open source community means that you can simply always _start_ with the optimal setup.

## Summary

In closing, I think that docker is the perfect tool for database management within your projects and as a database is more often than not the essential foundation for an application or website, learning and using this approach is the perfect foot-in-the-door for any developer to see the expansive landscape of what docker is capable of. In all that we have detailed out and explained here, we've barely touched on even 1% of what can be done. 

I honestly urge you to try it out with your next project, because after a while of using it you will begin to see that so much is possible thanks to this platform.
