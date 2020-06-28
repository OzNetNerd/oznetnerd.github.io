---
title: "Network Automation with Cisco Modeling Labs (CML2)"
sub_heading: "Setting up your environment"

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- NetOps
- Cisco
- Python
- Netmiko
- Network Automation

tags:
- NetOps
- Cisco
- Python
- Netmiko
- Network Automation
---

Coming from a network engineering background, network automation has always interested me. In fact, it was network automation which spiked my interest in DevOps. The beauty of being able to make safe, repeatable and automated changes at scale cannot be overstated. But I digress.

Cisco recently released CML2 (formerly known as VIRL). As per the [release notes](https://www.cisco.com/c/en/us/td/docs/cloud_services/cisco_modeling_labs/v200/release/notes/b_cml_release_notes_2-0/m_overview.html), they've made a number of enhancements and added new features too. 

Being a DevOps guy, of course the **"APIs and Programmability"** section caught my eye. I found this line interesting in particular: _"You can also use the APIs to start and stop nodes or to break connections in the running simulation, which facilitates writing Network DevOps tests of failure scenarios"_. The reason being that it is reminiscent of my [Test Driven Develeopment](/tags/#tdd) posts. Being able to easily test network changes before rolling them out in production is simply brilliant. 

With that, I was hooked. I purchased a copy of CML2 and spun it up yesterday.

## Installation

Installation is very simple. Following the ["Installing CML 2.0"](https://www.cisco.com/c/en/us/td/docs/cloud_services/cisco_modeling_labs/v200/quick/start/b_cml_quick_start_2-0/m_deploy_and_configure.html) and ["Initial Set-up"](https://www.cisco.com/c/en/us/td/docs/cloud_services/cisco_modeling_labs/v200/quick/start/b_cml_quick_start_2-0/m_initial_configuration.html) guides will have you up and running in no time.

To make life **a lot** easier, make sure you do the following:
1. Set a static IP address on your hypervisor (ESXi, Workstation or Fusion)
2. Set a static IP address on CML

## Breakout

Once you've got CML up and running, it's time to set up the Breakout. As per the docs:

_"The breakout utility allows for users to use their favorite external terminal emulator like Putty, Kitty, SecureCRT, etc. to devices in CML2"_

Our automation techniques are covered by the "etc". Paramiko, Netmiko, Ansible, NAPALM, nornir, etc. We can use them all, thanks to the Breakout. So without further ado, let's set it up.

1. Log into CML2 and click `Tools` -> `Breakout Tool`. 
2. Scroll to the bottom of the page and download the executable.
3. Create a `config.yaml` file in the directory where you downloaded the executable. Paste the following text into the file:
    ```yaml
    console_start_port: 9000
    controller: <CML_IP> # change this to your CML's static IP
    extra_lf: false
    lab_config_name: labs.yaml
    populate_all: true
    ui_server_port: 8080
    username: admin
    verify_tls: false
    vnc_start_port: 5900
    listen_address: 127.0.0.1 # Remove this line to make the Breakout network accessible 
    ``` 

4. Set the following environment variable. For Windows, the command is:

    ```
    set BREAKOUT_PASSWORD=<CML_PASSWORD>
    ```

    **Note:** Though the password can be set in the `config.yaml` file, it shouldn't be done. Storing clear text passwords in files is **very** insecure. 
    {: .notice}
  
5. Run the Breakout executable with the `ui` flag:

    ```
    breakout-windows-x86_amd64.exe ui
    ```

6. Browse to the Breakout's web console. It will either be at `http://127.0.0.1:8080` or `http://<YOUR_IP_ADDRESS>:8080`.
  
    All of your CML2 labs will be listed here. To enable your automation tool(s) to access them, flick the "Status" switch to on.   

7. Ensure that the Breakout connection works by SSH'ing into a lab device. 

## Automation example

Now for the moment we've all been waiting for! Let's see if we can access our CML2 environment programatically:

{% highlight python linenos %}
from netmiko import ConnectHandler

breakout_host_ip = "192.168.20.3"
cmd = "show ip int brief"

iosv_1 = {
    "device_type": "cisco_ios_telnet",
    "host": breakout_host_ip,
    "port": 9000,
    "global_delay_factor": 2,
}

print(f"Connecting to: {host_ip}...")
net_connect = ConnectHandler(**iosv_1)
print("Connected!")
print(f"Executing command:\n{cmd}")
sh = net_connect.send_command(cmd)
print(sh)
{% endhighlight %}

And now let's run the script:

```
python3 run.py
Connecting to: 192.168.20.3...
Connected!
Executing command:
show ip int brief
Interface                  IP-Address      OK? Method Status                Protocol
GigabitEthernet0/0         unassigned      YES unset  administratively down down    
GigabitEthernet0/1         unassigned      YES unset  administratively down down    
Router#

Process finished with exit code 0
```

## Reference URLs

For easy reference, here are the details you'll need once your environment is up and running:

* **CML2 Administration Console**
  * **URL:** `https://<CML_IP>/`
  * **Username:** `admin`
* **System Administration Cockpit**
  * **URL:** `https://<CML_IP>:9090`
  * **Username:** `sysadmin`
* **Breakout**
  * **URL:** `http://<BREAKOUT_HOST_IP>:8080`

## Closing thoughts

And there you have it! You're CML2 environment is set up and ready to go. In my next post I'll dive deeper on network automation.

**Update:** The next post, [Multithreading with Python and Netmiko](/2020-06-28-multithreading-with-python-netmiko/), has been published.