---
title: 'Kubernetes from the Ground Up: What is it?'
sub_heading: "Add orchestration to your containerisation"
redirect_from: /kubernetes-from-the-ground-up-what-is-it/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Kubernetes
- DevOps
- Docker

tags:
- DevOps
- Docker
- Kubernetes
- Orchestration
---
If you've looked into containers before, you've likely heard the name Kubernetes. This post will tackle what it is at a high level, while subsequent posts will delve deeper into the details.

Let's kick off this post with a couple of quotes from the [**Kubernetes website**](https://kubernetes.io/):

> Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications.
> 
> It groups containers that make up an application into logical units for easy management and discovery. Kubernetes builds upon 15 years of experience of running production workloads at Google, combined with best-of-breed ideas and practices from the community.

There's a lot of great information in this quote, so let's go ahead and take a closer look.

## Container Orchestration

> Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications.

Tools which handle the deployment, scaling, and management of containerised applications are known as Container Orchestrators. They actually do a lot more than this as you'll see in this series of posts.

Using an orchestrator makes life a lot easier for everyone. For example, instead of having to manually locate servers which have sufficient resources to run a new application, the orchestrator can do that for you.

Alternatives to Kubernetes include Docker Swarm, Nomad, Marathon and Mesos, to name a few.

> It groups containers that make up an application into logical units for easy management and discovery.

These logical units are known as [**microservices**](https://en.wikipedia.org/wiki/Microservices). While breaking applications up into microservices provides numerous advantages over monolithic applications,  doing so does come with its own problems. The two major ones being management and communication between containers.

Management becomes an issue because we don't have that one to one relationship between a single application and a single server, or a single VM, the way we do with monoliths. Instead, we have multiple microservices working together to form a single application. These microservices can reside on different hosts, different clusters and/or in different data centres. How can we manage an application if we don't actually know where it resides?

This leads us to the "communication between containers" issue, which is actually a three part problem:

1.  With containers being spread far and wide, how do we ensure full connectivity between them all?
2.  How do we implement security when we don't know where each container is deployed?
3.  With applications being broken up into multiple microservices, how do the microservices discover one another?

To solve these problems we use features provided by Kubernetes directly and/or tools which can be deployed alongside it. For example, using _deployments_, _tags_, a _Container Network Interface (CNI)_ such as Calico and _service mesh_ such as Istio.

_**Note:** Don't worry if the preceding paragraph is gibberish to you, we'll go through each of these in more detail in the next few posts of this series._

> Kubernetes builds upon 15 years of experience of running production workloads at Google, combined with best-of-breed ideas and practices from the community.

Though Kubernetes was officially released on the 7th of June 2014, it is based on an internal Google project called Borg, which has been in production for the past 15 years. After releasing Kubernetes, Google handed over the reins to the Cloud Native Computing Foundation (CNCF) who are now responsible for the project.

_Note: For additional information on orchestrators, see the "Virtualisation has too much bloat and not enough portability – All hail containers!" section of the [**DevOps: The Evolution of Applications**](/devops-the-evolution-of-applications/) post._

## More Information

For more information about what Kubernetes is, and what it isn't, take a look at the [**What is Kubernetes?**](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/) page.

## Update

The next post in this series, [**Choosing a configuration method**](/kubernetes-from-the-ground-up-choosing-a-configuration-method/), is now live.