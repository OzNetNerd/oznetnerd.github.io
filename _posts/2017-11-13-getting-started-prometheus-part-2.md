---
title: Getting Started with Prometheus - Part 2
sub_heading: "Once you go with Prometheus, you won't go back!"
redirect_from: /getting-started-prometheus-part-2/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Docker
- Monitoring

tags:
- Grafana
- DevOps
- Docker
- Prometheus
---
As you've probably guessed by [**Docker posts**](/tags/#docker), I'm a huge fan of containerisation. Therefore instead of installing Prometheus on a host, let's instead spin it up in a container. [**As described**](https://prometheus.io/docs/prometheus/latest/installation/) on the Prometheus website, we can accomplish this by issuing only a single command:

```
docker run -p 9090:9090 -v /tmp/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
```

Let's break down each component of this command to make sure we fully understand what it is doing:

*   `docker run`: Spin up a container
*   `-p 9090:9090`: Bind a port on our Docker host to a container port. This enables devices outside of the Docker host to reach the container on port 9090
*   `-v /tmp/prometheus.yml:/etc/prometheus/prometheus.yml`: Binds the `/tmp/prometheus.yml` file stored on the Docker host to `/etc/prometheus/prometheus.yml` inside of the container
*   `prom/prometheus`: The `/` that we want to use

Before we move on, it's important to note that the binding mentioned in the third port above is not a copy and paste of the file, but rather a symbolic link. That is to say, only a single copy of the file exists. Further to this, both the Docker host and the container can edit it.

Now, if we try to issue the command as is, we'll receive the following error message:

```
container_linux.go:265: starting container process caused "process_linux.go:368: container init caused \"rootfs_linux.go:57: mounting \\"/tmp/prometheus.yml\\" to rootfs \\"/var/lib/docker/overlay2/bb59496a0b1d48681ed47bdce140260fb9accbb3ad31cf838f42e06b493b2b00/merged\\" at \\"/var/lib/docker/overlay2/bb59496a0b1d48681ed47bdce140260fb9accbb3ad31cf838f42e06b493b2b00/merged/etc/prometheus/prometheus.yml\\" caused \\"not a directory\\"\""
docker: Error response from daemon: oci runtime error: container_linux.go:265: starting container process caused "process_linux.go:368: container init caused \"rootfs_linux.go:57: mounting \\"/tmp/prometheus.yml\\" to rootfs \\"/var/lib/docker/overlay2/bb59496a0b1d48681ed47bdce140260fb9accbb3ad31cf838f42e06b493b2b00/merged\\" at \\"/var/lib/docker/overlay2/bb59496a0b1d48681ed47bdce140260fb9accbb3ad31cf838f42e06b493b2b00/merged/etc/prometheus/prometheus.yml\\" caused \\"not a directory\\"\""
: Are you trying to mount a directory onto a file (or vice-versa)? Check if the specified host path exists and is the expected type.
ERRO[0001] error waiting for container: context canceled
```

That's because, as per the error message, we don't actually have a `prometheus.yml` file yet. Let's go ahead and [**grab this one**](https://prometheus.io/docs/prometheus/latest/getting_started/), which is pasted below for convenience:

```
global:
  scrape_interval:     15s # By default, scrape targets every 15 seconds.

  # Attach these labels to any time series or alerts when communicating with
  # external systems (federation, remote storage, Alertmanager).
  external_labels:
    monitor: 'codelab-monitor'

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=` to any timeseries scraped from this config.
  - job_name: 'prometheus'

    # Override the global default and scrape targets from this job every 5 seconds.
    scrape_interval: 5s

    static_configs:
      - targets: ['localhost:9090']
``` 

You might notice something quite interesting here. In the [**previous post**](/getting-started-prometheus-part-1/) it was mentioned that Scraping is the primary method Prometheus uses to obtains host metrics. In fact, as per the configuration file above, it is the way that Prometheus gathers statistics on itself too!

After copying and pasting this configuration to `/tmp/prometheus.yml` on our Docker host, we should be good to go. However, instead of using the command provided by the Prometheus website, let's use this one instead:

docker run -p 9090:9090 -d --rm --name prom -v /tmp/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus

These additional options achieve the following:

*   `-d`: Run as a daemon. This enables us to continue using our CLI session while the container runs in the background
*   `--rm`: Deletes the container once we stops running. This is useful for when you're playing around with Docker and/or Prometheus
*   `--name`: Names the container

To check on our container, we can use the `docker logs prom` and  `docker ps -l` commands, like so:

```
will@ubuntu:~$ sudo docker logs prom
time="2017-11-12T01:23:59Z" level=info msg="Starting prometheus (version=1.8.1, branch=HEAD, revision=3a7c51ab70fc7615cd318204d3aa7c078b7c5b20)" source="main.go:87" 
time="2017-11-12T01:23:59Z" level=info msg="Build context (go=go1.9.1, user=root@ab78fb101775, date=20171023-15:50:57)" source="main.go:88" 
time="2017-11-12T01:23:59Z" level=info msg="Host details (Linux 4.4.0-98-generic #121-Ubuntu SMP Tue Oct 10 14:24:03 UTC 2017 x86_64 38ed2beb638c (none))" source="main.go:89" 
time="2017-11-12T01:23:59Z" level=info msg="Loading configuration file /etc/prometheus/prometheus.yml" source="main.go:254" 
time="2017-11-12T01:23:59Z" level=info msg="Listening on :9090" source="web.go:341" 
time="2017-11-12T01:23:59Z" level=info msg="Loading series map and head chunks..." source="storage.go:428" 
time="2017-11-12T01:23:59Z" level=info msg="0 series loaded." source="storage.go:439" 
time="2017-11-12T01:23:59Z" level=info msg="Starting target manager..." source="targetmanager.go:63" 
time="2017-11-12T01:23:59Z" level=info msg="Server is Ready to receive requests." source="main.go:230" 

will@ubuntu:~$ sudo docker ps -l
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
38ed2beb638c        prom/prometheus     "/bin/prometheus -..."   5 minutes ago       Up 5 minutes        0.0.0.0:8080->9090/tcp   prom
```

And because Prometheus posts metrics about itself at `http://:9090/metrics` , we can browse to this address to see what data it is providing. Below is an excerpt of what you will see:

```
# HELP go_gc_duration_seconds A summary of the GC invocation durations.
# TYPE go_gc_duration_seconds summary
go_gc_duration_seconds{quantile="0"} 5.1544e-05
go_gc_duration_seconds{quantile="0.25"} 0.000130581
go_gc_duration_seconds{quantile="0.5"} 0.000171943
go_gc_duration_seconds{quantile="0.75"} 0.000252953
go_gc_duration_seconds{quantile="1"} 0.015861303
go_gc_duration_seconds_sum 1.8412401489999999
go_gc_duration_seconds_count 2208
# HELP go_goroutines Number of goroutines that currently exist.
# TYPE go_goroutines gauge
go_goroutines 214
# HELP go_memstats_alloc_bytes Number of bytes allocated and still in use.
# TYPE go_memstats_alloc_bytes gauge
go_memstats_alloc_bytes 2.1317796e+08
# HELP go_memstats_alloc_bytes_total Total number of bytes allocated, even if freed.
# TYPE go_memstats_alloc_bytes_total counter
go_memstats_alloc_bytes_total 8.253661464e+10
# HELP go_memstats_buck_hash_sys_bytes Number of bytes used by the profiling bucket hash table.
# TYPE go_memstats_buck_hash_sys_bytes gauge
go_memstats_buck_hash_sys_bytes 1.926848e+06
# HELP go_memstats_frees_total Total number of frees.
# TYPE go_memstats_frees_total counter
go_memstats_frees_total 1.128395565e+09
```

## Queries

Now that we've got Prometheus up and running, we can start looking at writing Queries. A query, as its name suggests, is simply a database query. Using queries you can obtain an original copy of the data which has been stored, or you can apply functions to it such as aggregations. This flexibility allows you to monitor and graph information which is most important to you.

The Prometheus website covers queries in multiple locations throughout their documentation:

*   **[Getting Started](https://prometheus.io/docs/prometheus/latest/getting_started/)**
*   [**Querying: Basics**](https://prometheus.io/docs/prometheus/latest/querying/basics/)
*   [**Querying: Operators**](https://prometheus.io/docs/prometheus/latest/querying/operators/)
*   [**Querying: Functions**](https://prometheus.io/docs/prometheus/latest/querying/functions/)
*   [**Querying: Examples**](https://prometheus.io/docs/prometheus/latest/querying/examples/)

Using these queries, as well as the information Prometheus is collecting about itself, we can head over to `http://:9090/graph` and start putting what we've learned into action.

Once we've gotten the hang of that, we can take things to the next level by graphing our data in Grafana. If you're not sure how to do that, stay tuned as it will be covered in a post in the near future.