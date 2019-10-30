---
title: 'DevOps: The Evolution of Applications'
sub_heading: "From one server per app to serverless apps"
redirect_from: /devops-the-evolution-of-applications/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- DevOps

tags:
- DevOps
- Kubernetes
- Orchestration
- Serverless
---
## One server per application

In the not too distant past, it was normal to have a one to one relationship between applications and servers. For example, your mail and web applications would reside on two separate, physical servers.

This approach was greatly inefficient and costly too. Each server would be purchased with years of predicted growth in mind. This resulted in racks full of overly powerful and underutilised servers which would drive up cooling, power and data centre space costs.

Furthermore, applications would often fall short of their forecasted growth, leaving businesses in a stalemate. They would want to move their applications to smaller servers to save on costs, but they can't guarantee they won't break their applications in the process. If they do break, then that’ll end up costing the business too. It really was a lose-lose situation.

## All hail virtualisation!

Virtualisation was the answer to businesses' prayers. By allowing multiple applications (virtual machines) to run on a single server, it solved the problems experienced with the one server per application approach. High powered servers would no longer have their resources sitting idly by. They could now be max'ed out to within an inch of their lives.

In addition to this, using tools like vMotion made application migrations extremely painless. If a server was being decommissioned, upgraded or downgraded, migrating its VMs to another host was as easy as a couple of mouse clicks.

### Where did my app go?

When VMware released their Distributed Resource Scheduler (DRS), it was met with some apprehension. DRS, in a nutshell, allows the hypervisor to automatically relocate VMs across a cluster to ensure that their resource requirements could be fulfilled.

The reason for the apprehension was due admins being used to the idea of having complete knowledge of which workloads resided where. The thought of handing the reins over to RDS was concerning for some. However, when weighing up the pros and perceived cons, it was easy to see that RDS was too good  a features to pass up.

## Virtualisation has too much bloat and not enough portability - All hail containers!

Just when we thought VMs were the bee’s knees, [**containers**](/?s=docker) started to gain traction. While VMs allow us to run multiple applications on a single host, each one of those applications requires a full OS install. Sure, there are ways to reduce storage usage by implementing cloning, deduplication and/or compression on entreprise SANs, but SANs aren’t always deployed alongside hypervisors.

The other problem is portability. While vMotion can move VMs between hosts in a cluster, as well as between hosts in separate clusters, it isn’t enough to meet today’s demands. **Businesses need to be able to move their applications to any platform, be it on premises or in the cloud, with complete ease.**

Containerisation provides solutions to both of these problems. Containers solve the OS bloat issue by using layered images. Each one consists of one or more multiple read-only layers, and one read-write layer on top. These "layers" can be anything. For example, the Ubuntu, git, nginx, etc.

If we had a container which consisted of these three layers, we would be provided with a Ubuntu installation which had git and nginx installed. It would look and feel exactly like a VM. If we then spun up a second container, we would have two of them which looked and felt like two VMs. The difference being that the containers would have a much smaller footprint because they share the same read-only Ubuntu, git and nginx layers.

If the containers needs to make changes to the read-only layers, the change is actually made on the read-write layer for that particular container. This means that the integrity of the read-only layer remains in tact and prevents one container from affecting another.

In regards to portability, containers are, well… self contained. They consist of everything they need to run. This enables businesses to spin up containers on any platform, anywhere in the world, with a single command. And because the dependencies are stored within the container, it’s **guaranteed** to work every single time. This is in stark contrast to traditional applications which often depend on other packages, and sometimes, specific versions of those packages, be installed before the application can run.

You might be wondering, what’s containerisation’s answer to DRS? The answer is known as a scheduler. Schedulers are often part of container [**orchestration tools, such as Kubernetes**](/kubernetes-from-the-ground-up-what-is-it/). In a nutshell (as you can tell, I really enjoy saying “in a nutshell”), schedulers look at a container’s requirements and place them on a host which has enough resources to satisfy its needs.

It must be noted, however, that DRS and schedulers go about performing their tasks in very different ways. While DRS will relocate a VM to another host, the [**Kubernetes scheduler destroys pods**](https://kubernetes.io/docs/tasks/administer-cluster/out-of-resource/) (think of pods as containers for now), and spins up a fresh copy on another host. This might sound a little drastic, but it's actually very normal in the world of containers because they are ephemeral in nature.

## I’m still not happy, give me more - All hail Serverless!

If you thought containerisation was the final chapter in this story, you were wrong! Containers, as well as all of the other solutions we’ve discussed thus far, require someone to do the following:

*   Ensure there’s enough server resources to meet demand
*   Ensure the solution is scalable
*   Maintain the patches of the underlying operating system(s)
*   The application is configured on the host/hypervisor/orchestrator

Serverless on the other hand, requires none of this. It enables developers to do what they do best - develop. When the application is ready, the developers are able to publish it without having to worry about physical servers, operating systems, load balancers, etc. To them, it’s as if the servers don’t even exist (hence the name serverless). This is all handled by the cloud provider transparently.

Another advantage of serverless is how it’s billed. With all of the other approaches covered in this post, users get charged for idle time. Serverless on the other hand, is only charged when the script is executed. That is to say, you only pay for what you use, and idle time is free.

## Which approach should I use?

_In a nutshell_, I’d recommend a mix of containers and serverless. Given that serverless offerings typically have a maximum execution time of 5 minutes, containers should be used for long running processes, as well as established applications which cannot be easily ported to serverless. Serverless should then be used for everything else.

## Stay tuned!

In this post we touched on some pretty cool topics, such as container orchestration and serverless, but we barely scratched the surface. Stay tuned for the next posts which will delve deeper into both of these technologies.