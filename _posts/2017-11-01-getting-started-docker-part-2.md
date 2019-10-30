---
title: Getting Started with Docker - Part 2
sub_heading: "It's time we look at containerisation"
redirect_from: /getting-started-docker-part-2/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Docker

tags:
- DevOps
- Docker
---
In [**the previous post**](/getting-started-with-docker-part-1/) we may have started running before we could walk. In this post we'll first take a few steps back to make sure we cover the basics before diving deeper.

## Building an image

When we examined the `python:3.6.3-alpine3.6` [**image's dockerfile**](https://github.com/docker-library/python/blob/3f12f511910098f45951111aa5642fd935133afc/3.6/alpine3.6/Dockerfile), we saw the components which are used to create a Docker image:

*   `FROM alpine3.6`
*   `ENV` commands
*   `RUN` commands
*   A **single** `CMD` command

OK, so we already know that the `FROM alpine3.6` command means that the Python image is going to use the  **Apline 3.6** as its Linux operating system. `ENV`, as its name suggests, is used to specify environment variables for the image. That leaves us with `RUN` and `CMD`.

If you've used Linux, it's highly likely that the `RUN` commands look familiar. Here's why - [**as described in this GoinBigData post**](http://goinbigdata.com/docker-run-vs-cmd-vs-entrypoint/), when `RUN` commands are written in the ` ` style (known as shell form), they're executed using `/bin/sh -c`. In other words, they're standard Linux commands/scripts. (If you don't know the difference between `sh` and `bash`, have a read of [**this StackOverflow question**](https://stackoverflow.com/questions/5725296/difference-between-sh-and-bash).)

To sum up what we've just covered - Docker Engine examines the `dockerfile`, spins up a Linux OS (Alpine in this instance), runs a bunch of `sh`  commands and sets a few environment variables. It really is that simple.

OK, now let's now move on to the final piece of the puzzle - the `CMD` command. As described in the GoinBigData post linked to above, the `CMD ["python3"]` style is known as "exec form". Instead of running through `/bin/sh -c`, exec form runs the executable directly, which in this case is `python3`.

You're probably thinking, apart from the way in which they're executed, what is the difference between `RUN` and `CMD` commands? Why can we run multiple `RUN` commands, and only a single `CMD` command?

Docker Engine will execute `RUN` commands during the creation of an image. For example, it will install the Python, create symbolic links, remove unnecessary files, etc, as part of the image creation process. Once done, the image is ready to be used as a container, and this is where the `CMD` command comes into play.

**Warning:** Hold onto your hat, we're about to go deep again!

## CMD

In Part 1 of this series I said that _"When done correctly, containers only have a single concern (formally referred to as a single process)"_. In the case of the Python image we've been dissecting, that's exactly what the `CMD` takes care of.

Let's spin up another container:

```
will@ubuntu:~$ sudo docker run -d --rm --name mypy python:3.6.3-alpine3.6 
75baeb52916d44e15b881ab4fb89c5e3e1283cc08cc0f5b5de8ea13209a8335a
```

In the above command, the flags used achieve the following:

*   `-d`: Makes the container a daemon. This is useful for us in this instance because we don't want to be dropped into the Python interpreter.
*   `--rm`: Delete's the container as soon as it is stopped

Now that we've got our container up and running and we're still on our Docker Host (as opposed to the Python interpreter), let's do some poking around.

```
will@ubuntu:~$ sudo docker ps
CONTAINER ID        IMAGE                              COMMAND                  CREATED             STATUS                PORTS             NAMES
75baeb52916d        python:3.6.3-alpine3.6             "python3"                8 minutes ago       Up 8 minutes                            mypy
```

We can see the `python3` command was issued after the container was launched. That's fine, we expected to see that. Let's now see what the container has to say for itself:

```
will@ubuntu:~$ sudo docker exec mypy ps
PID   USER     TIME   COMMAND
    1 root       0:00 python3
    7 root       0:00 ps
```

The container is running only running `python3` and the `ps` command we've just issued. That's it. As fascinating as that is, what's more interesting is the fact that `python3` is running as `PID 1`. I won't bore you with the details, but in a nutshell it is _"a process that must be running at all times, if this process ends, the kernel enters into a panic mode, after which you cannot do anything else, except rebooting"_ ([**reference**](https://felipec.wordpress.com/2013/11/04/init/)).

In other words, the `CMD` command tells Docker what it needs to keep track of in order to determine whether a container should stay up or be brought down. This brings us back to why we needed the `-it` flags when spinning up our Python container. Without them, Docker does the following:

1.  Spins up the container
2.  Launches the `python3` executable
3.  Python sees that it's got nothing to process so it gracefully terminates
4.  Docker receives the `exit 0` exit code from Python and then shuts down the container

We can see this when we spin up `mypy2` without the `-it` or `-d` flags:

```
will@ubuntu:~$ sudo docker run --name mypy2 python:3.6.3-alpine3.6 
will@ubuntu:~$ sudo docker ps -a
CONTAINER ID        IMAGE                              COMMAND                  CREATED             STATUS                     PORTS           NAMES
2342211efc48        python:3.6.3-alpine3.6             "python3"                4 seconds ago       Exited (0) 2 seconds ago                   mypy2
```

## Using the container

Being dropped into the Python interpreter isn't all that useful, nor is having the container spun up only to be brought down shortly thereafter. Having Docker execute a "hello" message is much more useful, so let's go ahead and do that now:

```
sudo docker run --name mypy3 --rm -v "$PWD"/hi.py:/hi.py python:3.6.3-alpine3.6 python3 /hi.py
hello!
```

_Side Note: Notice how in Part 1 we didn't have to specify the_ `python` _executable after the image name, but this time we did? That's because specifying anything after the image name overrides the_ `CMD` _command in the Dockerfile. I'll cover this in more detail in Part 3._

What we've done with the above command is called a [**bind mount**](https://docs.docker.com/engine/admin/volumes/bind-mounts/). Bind mounts allow us to mounted a file or directory on the Docker Host to a file or directory inside of our container. That is to say, the `hi.py` file resides on our Docker Host machine, but we've made it available inside of our container too. We've then used Python inside of the container to run the bound `hi.py` file.

In the interest of keeping this post to a reasonable length, I'll leave storage at that for now and will tackle it further in a future post.

## Summary

Let's run through what we've learned in this post:

*   `dockerfiles` are used to tell Docker Engine how to create our image
*   For the most part, `dockerfiles` are made up of `sh` scripts/commands
*   Images are always based on other images, e.g the Python image we've been discussing is based on Alpine Linux
*   We can only have `CMD` command per `dockerfile`
*   The `CMD` command tells Docker which executable to monitor