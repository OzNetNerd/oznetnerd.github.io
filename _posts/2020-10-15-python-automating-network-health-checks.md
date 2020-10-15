---
title: "Python: Automating network health checks"
sub_heading: "Automating repeatable tasks saves massive amounts of time"

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Automation
- Python

tags:
- Automation
- Python
- Networking

---

I was a network engineer in a previous life. Though I've moved into DevOps, [my love for network automation](https://oznetnerd.com/2016/11/27/why-automate/) has not subsided.

Recently, an engineer posted a message on one of the Slack Workgroups I frequent. He had been asked to test and document network connectivity for a large number of hosts... **by close of business the next day**. Given the tight timeline, I offered to write a script for him which would automate the process. He graciously accepted.

## Design

`ping` and `traceroute` tests can take anywhere from a few seconds to 10s of seconds **each**. Given that the engineer needed to test a large number of hosts, running these commands serially would take a very long time. So what can we do to fix this problem? Use multithreading to run the test in parallel.

If this design sounds familiar, it might be because it was covered in a previous post - [Multithreading with Python and Netmiko](2020/06/28/multithreading-with-python-netmiko/). However, this time we're going to try something a little different. In the aforementioned post, we used the [`threading` module](https://docs.python.org/3/library/threading.html). 

This time we'll be using the [`concurrent.futures` module](https://docs.python.org/3/library/concurrent.futures.html). The technical explanation is that it provides a higher level interface than `threading`. This means that it abstracts away a lot of the implementation details required to implement multithreading.

To put it another way, `concurrent.futures` is very lightweight as we'll see in a moment. And comparatively speaking, `threading` requires quite a lot of code to utilise.

## Code

The finished script can be [found on GitHub](https://github.com/OzNetNerd/Network-Health-Check). Let's now dissect the script to ensure we understand how it works.

[First](https://github.com/OzNetNerd/Network-Health-Check/blob/7839d2bbe344a2c37cf84f1fdda263bfc4569bff/src/net-hc.py#L65-L70) we create the `OUTPUT_DIR`. We then read the file which contains the hostnames that need to be checked:

{% highlight python linenos %}
def main():
    print('Running health checks...')
    Path(OUTPUT_DIR).mkdir(parents=True, exist_ok=True)

    with open(INPUT_FILENAME, 'r') as f:
        ips = f.read().splitlines()
{% endhighlight %}

[Next](https://github.com/OzNetNerd/Network-Health-Check/blob/7839d2bbe344a2c37cf84f1fdda263bfc4569bff/src/net-hc.py#L72-L73) we run `health_checks` using `ThreadPoolExecutor` (which comes from the `concurrent.futures` module).

{% highlight python linenos %}
    with ThreadPoolExecutor(max_workers=MAX_THREADS) as executor:
        [executor.submit(health_checks, ip) for ip in ips]
{% endhighlight %}

And just like that, we've implementing multithreading. That's right! All it took was **just two lines of code**. How amazing is that? 

Let's now move our focus to the [final piece of the puzzle](https://github.com/OzNetNerd/Network-Health-Check/blob/7839d2bbe344a2c37cf84f1fdda263bfc4569bff/src/net-hc.py#L31-L48) - the `health_checks` function:

{% highlight python linenos %}
def health_checks(ip):
    ping_cmd = f'{PING_COMMAND} {ip}'
    trace_cmd = f'{TRACEROUTE_COMMAND} {ip}'

    ping_status = run_command(ping_cmd)
    trace_status = run_command(trace_cmd)

    filename = f'{OUTPUT_DIR}{os.sep}{ip}.txt'
    with open(filename, 'w') as f:
        f.write(title('Ping Results'))
        f.write(ping_status)

        f.write(title('Trace Results'))
        f.write(trace_status)

    logging.info(f'Wrote outputs to: {filename}')
    logging.debug(ping_status)
    logging.debug(trace_status)
{% endhighlight %}

Though this function is the heart of our script, there's nothing particularly exciting happening here. We're simply running the `traceroute` and `ping` commands on each the provided hosts. The real beauty comes from the fact that this function is being called by `ThreadPoolExecutor`. 

In doing so, we're able to test connectivity to a large number of hosts in the **same amount of time** that it would take us to test one host.

## Output

Below is an example of what the health check output looks like:

```
****************************************************************************************************
Ping Results
****************************************************************************************************
PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data.

--- 1.1.1.1 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2004ms
rtt min/avg/max/mdev = 10.165/11.043/12.695/1.168 ms

****************************************************************************************************
Trace Results
****************************************************************************************************
traceroute to 1.1.1.1 (1.1.1.1), 30 hops max, 60 byte packets
 1  _gateway (192.168.20.1)  1.398 ms  4.752 ms  4.696 ms
 2  1.2.3.4 (1.2.3.4)  10.265 ms  11.023 ms  11.229 ms
 3  5.6.7.8 (5.6.7.8)  12.595 ms  17.532 ms  17.479 ms
 4  10.1.31.141 (10.1.31.141)  12.415 ms  12.358 ms  17.286 ms
 5  10.1.31.142 (10.1.31.142)  17.243 ms  17.187 ms  17.138 ms
 6  10.1.31.195 (10.1.31.195)  16.721 ms  12.358 ms  17.138 ms
 7  as13335.melbourne.megaport.com (103.26.71.38)  11.290 ms  11.685 ms  12.149 ms
 8  one.one.one.one (1.1.1.1)  11.860 ms  12.673 ms  12.618 ms
```