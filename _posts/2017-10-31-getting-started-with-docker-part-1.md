---
title: Getting Started with Docker - Part 1
sub_heading: "It's time we look at containerisation"
redirect_from: /getting-started-with-docker-part-1/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Docker

tags:
- DevOps
- Docker
---
For those new to Docker, you're probably wondering - What is it exactly?

_"Docker is the **company** driving the container movement and the only **container** platform provider to address every application across the hybrid cloud."_

OK, it's the name of a company. The next question is, what are containers? Rather than give you another quote, I'll give you my own definition...

## What are containers?

Containers are like [**portable executables**](https://en.wikipedia.org/wiki/Portable_Executable). You know, the applications that come as a standalone exe file and don't require installation? I draw this comparison for the following reasons:

1.  They are self-contained (pun intended!) units. That is to say, they come with everything they need in order to operate. You don't need to worry about installing dependencies the way you do with non-containerised applications, and they act exactly like a single executable from a user's perspective.
2.  They're disposable. When a portable application is misbehaving and/or you want to upgrade it, you simply delete it and start using a fresh copy. You can do exactly the same thing with containers.
3.  When done correctly, containers only have a [**single concern**](https://devops.stackexchange.com/questions/447/why-it-is-recommended-to-run-only-one-process-in-a-container) (formally referred to as a single process). Just as a portable version of Firefox is only concerned with providing the user with a web browser, an nginx container's only concern may be to provide a web server.

## What is Docker Engine?

OK so you know how I said containers don't require dependencies? Well, technically they've got one - Docker Engine. Docker Engine is used to create and run images and containers respectively, and is the tool you'll primary use when getting started with Docker.

## What do you mean, Self-Contained?

When those who aren't well versed with Linux hear that containers are self-contained, they think it's too good to be true. That's because they've suffered through the pain of trying to install tools which aren't available through their package managers. They've Googled for days, asked for advice on forums and made all sorts of file and system modifications. In the end they either give up, or they manage to get it working but have no idea how they did it.

If they were lucky enough to get it working, their joy soon turns sorrow again when they find themselves in [**dependency hell**](https://en.wikipedia.org/wiki/Dependency_hell) when they try to get the next tool up and running.

For those poor souls who are all too familiar with this situation, let me repeat - **containers are completely self-contained**. Each containers run its own, independent operating systems. As a result of this, each can install any version of any package they like without fear of breaking applications in other containers.

For example, let's look at how we can run Python in a container:

```
will@ubuntu:~$ sudo docker run -it --rm --name mypy python:3.6.3-alpine3.6
Unable to find image 'python:3.6.3-alpine3.6' locally
3.6.3-alpine3.6: Pulling from library/python
b56ae66c2937: Pull complete 
7c0ec3e02d22: Pull complete 
86a11b82fbba: Pull complete 
eda96460740c: Pull complete 
8b1d60d09519: Pull complete 
Digest: sha256:ca0ac7554c59442053b2978a875fe6d6f1bde342e811f950e65cea2a8b4df475
Status: Downloaded newer image for python:3.6.3-alpine3.6
Python 3.6.3 (default, Oct 26 2017, 05:20:20) 
[GCC 6.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> for i in range(5):
...     print('I can count! {0}'.format(i))
... 
I can count! 0
I can count! 1
I can count! 2
I can count! 3
I can count! 4
```

There we have it. With a single command we were able to stand up a container that was built to run Python. How cool is that?

## What does that command do?

Let's dissect the command we used to spin up our container:

```
sudo docker run -it --rm --name mypy python:3.6.3-alpine3.6
```

*   docker run: Runs Docker Engine
*   -it: Interactive & pseudo-TTY - Used to give us CLI access on the container
*   --rm: Delete's the container as soon as it is stopped
*   --name: Allows us to name our container
*   python: Specifies the [**Docker Hub**](https://hub.docker.com/)  the repository we want to use
*   3.6.3-alpine3.6: Specifies the tag of the image we want to use

_Note: For a full list of options, please see the [**Docker documentation**](https://docs.docker.com/engine/reference/commandline/run/)._

Next, let's have a look at the output that was produced as a result of the above command:

```
Unable to find image 'python:3.6.3-alpine3.6' locally
3.6.3-alpine3.6: Pulling from library/python
b56ae66c2937: Pull complete 
7c0ec3e02d22: Pull complete 
86a11b82fbba: Pull complete 
eda96460740c: Pull complete 
8b1d60d09519: Pull complete
```

When Docker says Unable to find image, it's telling us that the image is not on the machine yet. The subsequent 5 lines represent the downloading of the 5 layers which make up the python:3.6.3-alpine3.6 image.

Note that if we re-run the same command again, we get dropped directly into the Python interpreter because we now have a copy of the image locally:

```
will@ubuntu:~$ sudo docker run -it --rm --name mypy python:3.6.3-alpine3.6
Python 3.6.3 (default, Oct 26 2017, 05:20:20) 
[GCC 6.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

## Wait, what's this about layers?

Docker's use of layers are very well documented on [**their website**](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/), as well as by countless bloggers, so I'll try to make this section short, but sweet.

Dockerfiles are  used to tell Docker what it needs to do in order to create an image. For example, set environment variables, download a package, extract a tarball, etc.

Working backwards, to see the total size of the Python image (the sum of all layers), we issue the following command:

```
will@ubuntu:~$ sudo docker images
[sudo] password for wirobinson: 
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
python                        3.6.3-alpine3.6     7cf5797350ea        5 days ago          83.7MB
```

To see the layers which made up the image, we issue the following command:

```
will@ubuntu:~$ sudo docker history python:3.6.3-alpine3.6
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
7cf5797350ea        5 days ago          /bin/sh -c #(nop)  CMD ["python3"]              0B                  
 5 days ago          /bin/sh -c set -ex;   apk add --no-cache -...   5.75MB              
 5 days ago          /bin/sh -c #(nop)  ENV PYTHON_PIP_VERSION=...   0B                  
 5 days ago          /bin/sh -c cd /usr/local/bin  && ln -s idl...   32B                 
 5 days ago          /bin/sh -c set -ex  && apk add --no-cache ...   73.4MB              
 5 days ago          /bin/sh -c #(nop)  ENV PYTHON_VERSION=3.6.3     0B                  
 5 days ago          /bin/sh -c #(nop)  ENV GPG_KEY=0D96DF4D411...   0B                  
 5 days ago          /bin/sh -c apk add --no-cache ca-certificates   633kB               
 5 days ago          /bin/sh -c #(nop)  ENV LANG=C.UTF-8             0B                  
 5 days ago          /bin/sh -c #(nop)  ENV PATH=/usr/local/bin...   0B                  
 5 days ago          /bin/sh -c #(nop)  CMD ["/bin/sh"]              0B                  
 5 days ago          /bin/sh -c #(nop) ADD file:1e87ff33d1b6765...   3.97MB
``` 

And finally, if we're interested in seeing **exactly** what was done to create the final image, we can check the Dockerfile(s) themselves. For example, the python:3.6.3-alpine3.6 Dockerfile can be found [**here**](https://github.com/docker-library/python/blob/3f12f511910098f45951111aa5642fd935133afc/3.6/alpine3.6/Dockerfile). Note though as per the very first line in that file - FROM alpine:3.6 - our Python image is building on top of the Apline 3.6 image. Therefore, we'd need to look at [**its Dockerfile**](https://github.com/gliderlabs/docker-alpine/blob/0450fe826ccdedf63dd70da1ad953f1aaf932225/versions/library-3.6/x86_64/Dockerfile) as well.

It's worth noting that the 5 layers we see with sizes attributed to them in the last output above, are the same 5 layers we saw being downloaded on the initial run of the container.

On a side note, as per the Docker documentation _- The_ missing_ lines in the docker history output indicate that those layers were built on another system and are not available locally. This can be ignored._

## More on layers & Dockerfiles

For more, very useful information on layers and to get the most out of the image cache, have read of these articles:

*   [**Best practices for writing Dockerfiles**](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/)
*   [**About images, containers, and storage drivers**](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/)
*   [**Optimizing Docker Images**](https://www.ctl.io/developers/blog/post/optimizing-docker-images/)
*   [**Caching Docker Images**](https://www.ctl.io/developers/blog/post/caching-docker-images)
*   [**How to Maximize Your Docker Image Caching Techniques**](https://www.ctl.io/developers/blog/post/more-docker-image-cache-tips/)

## Summary

Wow, this post went a lot deeper than I had originally anticipated! Let's quickly run through what was covered.

*   Containers are self-contained units
*   Each container should do one job, and one job only
*   Images are made up of layers
*   All layers of an image are read only, except the top layer which is read & writable. This top layer is referred to as the container
*   The number of layers and what each of them do are determined by the contents of the dockerfile that is used to create them
*   Because all layers (bar the top one) is read only, they can be shared between containers resulting in great efficiencies
*   dockerfiles should be written in a way which optimises the cache
*   The --no-cache flag should be used when fresh data is required
*   Docker Engine is used to turn dockerfile instructions into an images. It is also used to run containers

## Update

[**Part 2**](/getting-started-docker-part-2/) of this series has been published.