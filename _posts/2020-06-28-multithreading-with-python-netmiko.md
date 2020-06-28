---
title: "Multithreading with Python and Netmiko"
sub_heading: "I feel the need... The need for speed!"

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- NetOps
- Cisco
- Netmiko
- Network Automation
- Python

tags:
- NetOps
- Cisco
- Python
- Netmiko
- Network Automation
---

If you ran the script in [the previous post](/2020/06/26/network-automation-with-cml2/), you will have found it took approximately 18 seconds to run. While that may not feel like a lot of time, what if we wanted to connect to five devices? Or ten? Our execution time will grow exponentially. What originally took **seconds** begins to take **minutes**.

So, how do we solve this problem? We use multithreading. In a nuthsell, mutlithreading enables us to run code concurrently as opposed to sequentially. As we'll see in this post, this enables us to carry out numerous tasks in the same time it'd take us to do one.

**Note:** For more information on concurrency, see this [Real Python article](https://realpython.com/python-concurrency/). 
{: .notice}  

## Writing a multithreaded script

In order to enable multithreading in our [previous script](/2020/06/26/network-automation-with-cml2/), we're going to have to make several modifications. Let's first take a look at what the finished script looks like, and then break it down one segment at a time:

{% highlight python linenos %}
from threading import Thread, currentThread, Lock
from queue import Queue
from datetime import datetime
from netmiko import ConnectHandler

NUM_THREADS = 3
PRINT_LOCK = Lock()

BREAKOUT_HOST_IP = "192.168.20.3"
BREAKOUT_PORTS = [9000, 9002, 9004]
COMMAND = "show ip int brief"

BASE_DEVICE_SETTINGS = {
    "device_type": "cisco_ios_telnet",
    "host": BREAKOUT_HOST_IP,
    "port": "",
    "global_delay_factor": 2,
}

def mt_print(msg):
    with PRINT_LOCK:
        print(msg)

def run_mt(mt_function, q, **kwargs):
    num_threads = min(NUM_THREADS, len(BREAKOUT_PORTS))

    for i in range(num_threads):
        thread_name = f'Thread-{i}'
        worker = Thread(name=thread_name, target=mt_function, args=(q, kwargs))
        worker.start()
        
    q.join()

def send_command(q, kwargs):
    command = kwargs['command']

    while True:
        thread_name = currentThread().getName()

        if q.empty():
            mt_print(f"{thread_name}: Closing as there's no jobs left in the queue.")
            return

        device_details = q.get()
        port = device_details["port"]

        mt_print(f"{thread_name}: Connecting to port {port}...")
        net_connect = ConnectHandler(**device_details)
        mt_print(f"{thread_name}: Connected!")
        mt_print(f"{thread_name}: Executing command:\n{command}")
        sh = net_connect.send_command(command)
        mt_print(f"{thread_name}: {port} output -\n{sh}")
        mt_print(f"{thread_name}: Done!")

        q.task_done()

def main():
    start_time = datetime.now()

    device_queue = Queue(maxsize=0)

    for port in BREAKOUT_PORTS:
        new_device = BASE_DEVICE_SETTINGS.copy()
        new_device["port"] = port
        device_queue.put(new_device)

    run_mt(mt_function=send_command, q=device_queue, command=COMMAND)

    print("\nElapsed time: " + str(datetime.now() - start_time))

if __name__ == '__main__':
    main()
{% endhighlight %}    


## Breaking it down

Now that we've seen the finished, let's break it down. Let's start with the imports:

{% highlight python linenos %}from threading import Thread, currentThread, Lock
from queue import Queue
{% endhighlight %}

These are the libraries we'll need. Here's how we'll be using them:

* **Thread:** Enables us to use multithreading
* **currentThread:** We'll use this for debugging
* **Lock:** Used to ensure threads don't interrupt one another (e.g both print a debug message at the same time)
* **Queue:** Works similarly to a `list`, but is much quicker when used with multithreading

Let's now specify our constants:

{% highlight python linenos %}
NUM_THREADS = 3
PRINT_LOCK = Lock()

BREAKOUT_HOST_IP = "192.168.20.3"
BREAKOUT_PORTS = [9000, 9002, 9004]
COMMAND = "show ip int brief"

BASE_DEVICE_SETTINGS = {
    "device_type": "cisco_ios_telnet",
    "host": BREAKOUT_HOST_IP,
    "port": "",
    "global_delay_factor": 2,
}
{% endhighlight %}

Above we've simply laid the groundwork for our script.

Next we make use of the `PRINT_LOCK` we created above:

{% highlight python linenos %}
def mt_print(msg):
    with PRINT_LOCK:
        print(msg)
{% endhighlight %}

By using this `mt_print` function instead of `print()` in our multithreaded code, we can ensure our `print` statements are not mangled.

Let's now move onto the heart of our script:

{% highlight python linenos %}
def run_mt(mt_function, q, **kwargs):
    num_threads = min(NUM_THREADS, len(BREAKOUT_PORTS))

    for i in range(num_threads):
        thread_name = f'Thread-{i}'
        worker = Thread(name=thread_name, target=mt_function, args=(q, kwargs))
        worker.start() 
    
    q.join()
{% endhighlight %}

This is where the magic happens. In this block of code we're:

1. Spinning up x number of threads
2. Naming them (for debugging purposes)
3. Passing the threads a function and keyword arguments
4. Starting the threads
5. `join`ing the threads

Joining the threads allows us to pause execution of the script here. Without this statement, Python would continue running `main()` while the threads are still executing. This means that our `Elapsed time` message would be printed before we had receive the results of our `show` commands.  

**Note:** If you've read up on multithreading previously, chances are you've heard of daemon threads. Daemon threads advise Python that it can terminate a script if the only thing left running are the threads. Because we don't use them in this script, Python will wait until all of our threads finish running before it terminates.   
{: .notice}

Though this next segment is our largest, it's actually quite straightforward:

{% highlight python linenos %}
def send_command(q, kwargs):
    command = kwargs['command']

    while True:
        thread_name = currentThread().getName()

        if q.empty():
            mt_print(f"{thread_name}: Closing as there's no jobs left in the queue.")
            return

        device_details = q.get()
        port = device_details["port"]

        mt_print(f"{thread_name}: Connecting to port {port}...")
        net_connect = ConnectHandler(**device_details)
        mt_print(f"{thread_name}: Connected!")
        mt_print(f"{thread_name}: Executing command:\n{command}")
        sh = net_connect.send_command(command)
        mt_print(f"{thread_name}: {port} output -\n{sh}")
        mt_print(f"{thread_name}: Done!")

        q.task_done()
{% endhighlight %}

As we can see in the `main()` function, the `send_command()` function is called by `run_mt()`. That is to say, `send_comand()` is going to be run concurrently by our threads.

Examining `send_comand()`, we can see a few things:
1. `while True` signifies an infinite loop. This ensures that our threads stay alive long enough to process all of the tasks in the queue
2. Speaking of the queue, we then see `if q.empty()`, `return`. This enables our threads to cease after the queue is empty
3. Finally, we see `q.task_done()`. This enables us to clear tasks from the queue

Now onto the final piece of the puzzle:

{% highlight python linenos %}
def main():
    start_time = datetime.now()

    device_queue = Queue(maxsize=0)

    for port in BREAKOUT_PORTS:
        new_device = BASE_DEVICE_SETTINGS.copy()
        new_device["port"] = port
        device_queue.put(new_device)

    run_mt(mt_function=send_command, q=device_queue, command=COMMAND)

    print("\nElapsed time: " + str(datetime.now() - start_time))


if __name__ == '__main__':
    main()
{% endhighlight %}

In this segment we:

1. Create the queue
2. Populate the queue with our devices settings
3. Start our threads

## Results

Now that we've got our multithreaded script finished, let's take it for a spin!

```
run2.py
Thread-0: Connecting to port 9000...
Thread-1: Connecting to port 9002...
Thread-2: Connecting to port 9004...
Thread-1: Connected!
Thread-1: Executing command:
show ip int brief
Thread-2: Connected!
Thread-2: Executing command:
show ip int brief
Thread-0: Connected!
Thread-0: Executing command:
show ip int brief
Thread-0: 9000 output -
Interface                  IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0         unassigned      YES unset  administratively down down    
GigabitEthernet0/1         unassigned      YES unset  administratively down down    
Router>
Thread-0: Done!
Thread-0: Closing as there's no jobs left in the queue.
Thread-2: 9004 output -
Interface                  IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0         unassigned      YES unset  administratively down down    
GigabitEthernet0/1         unassigned      YES unset  administratively down down    
Router>
Thread-2: Done!
Thread-2: Closing as there's no jobs left in the queue.
Thread-1: 9002 output -
Interface                  IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0         unassigned      YES unset  administratively down down    
GigabitEthernet0/1         unassigned      YES unset  administratively down down    
Router>
Thread-1: Done!
Thread-1: Closing as there's no jobs left in the queue.
Elapsed time: 0:00:18.482459


Process finished with exit code 0
```

And there we have it! Our script finished running in **18 seconds**. That is to say, **three** devices completed in the same amount of time it takes to do **one**. Had we done this without multithreading, it would have taken us approximately **one minute**. What a massive time saving.