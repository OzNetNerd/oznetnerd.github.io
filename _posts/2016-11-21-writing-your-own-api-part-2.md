---
title: Writing your own API, Part 2
sub_heading: "Retrieve structured data from your network devices"
redirect_from: /writing-your-own-api-part-2/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Ansible
- TextFSM
- Python
tags:
- Ansible
- Automation
- TextFSM
- DevOps
---
As I mentioned in [**my previous post**](/writing-your-own-api-part-1/), NTC-Ansible uses TextFSM templates to allow you to convert your CLI outputs to JSON and then access the data in an API-like fashion.

In this post I'll cover how to run TextFSM templates against CLI outputs.

## Downloading TextFSM

To get started, download the latest version of TextFSM using the **'git clone'** command:

```bash
will@ubuntu:/tmp$ git clone https://github.com/google/textfsm.git
Cloning into 'textfsm'...
remote: Counting objects: 105, done.
remote: Total 105 (delta 0), reused 0 (delta 0), pack-reused 105
Receiving objects: 100% (105/105), 65.15 KiB | 0 bytes/s, done.
Resolving deltas: 100% (45/45), done.
Checking connectivity... done.
```

## Running TextFSM

Navigate to the **'textfsm'** directory. Inside this directory is the textfsm.py  file and the **'examples'** directory:

```bash
will@ubuntu:/tmp$ cd textfsm/
will@ubuntu:/tmp/textfsm$ ls
clitable.py copyable_regex_object.py COPYING MANIFEST setup.py terminal_test.py textfsm.py texttable.py
clitable_test.py copyable_regex_object_test.py examples README.md terminal.py testdata textfsm_test.py texttable_test.py
```

To test TextFSM, simply use the following syntax:

```bash
python textfsm.py examples/_template examples/_example
```

There are two things to note here:

1.  The   must be the same for both the template and the example.
2.  The template's filename must be specified first, followed by the example's filename (as per the syntax above).

When executed correctly, your output will look similar to this:

```
will@ubuntu:/tmp/textfsm$ python textfsm.py examples/cisco_bgp_summary_template examples/cisco_bgp_summary_example 
FSM Template:
Value Filldown RouterID (\S+)
Value Filldown LocalAS (\d+)
Value RemoteAS (\d+)
Value Required RemoteIP (\d+(\.\d+){3})
Value Uptime (\d+\S+)
Value Received_V4 (\d+)
Value Status (\D.*)

Start
 ^BGP router identifier ${RouterID}, local AS number ${LocalAS}
 ^${RemoteIP}\s+\d+\s+${RemoteAS}(\s+\S+){5}\s+${Uptime}\s+${Received_V4} -> Record
 ^${RemoteIP}\s+\d+\s+${RemoteAS}(\s+\S+){5}\s+${Uptime}\s+${Status} -> Record

EOF


FSM Table:
['RouterID', 'LocalAS', 'RemoteAS', 'RemoteIP', 'Uptime', 'Received_V4', 'Status']
['192.0.2.70', '65550', '65551', '192.0.2.77', '5w4d', '1', '']
['192.0.2.70', '65550', '65552', '192.0.2.78', '5w4d', '10', '']
```

Everything up to and including the 'EOF' line is contained in the '**cisco_bgp_summary_template'** file, TextFSM is simply displaying it for your information. Everything after the 'EOF' line is the result of TextFSM running the template against the **'cisco_bgp_summary_example'** file.

(Note: If you want to view the contents of these two files without having to download the code, you can see it here: [**cisco_bgp_summary_template**](https://github.com/google/textfsm/blob/master/examples/cisco_bgp_summary_template) and [**cisco_bgp_summary_example**](https://github.com/google/textfsm/blob/master/examples/cisco_bgp_summary_example))

## Regular Expressions

As you can see, TextFSM is all about regular expressions. If you haven't come across regular expressions before, there is a massive amount of information available on the internet.

Once you've got a basic understanding of it, I suggest you flick through the other **[TextFSM templates](https://github.com/google/textfsm/tree/master/examples)** as well as the [**NTC-Ansible temaplates**](https://github.com/networktocode/ntc-templates/tree/master/templates) and then head on over to [**regex101**](https://regex101.com/) and use it to decode any bits of regex you don't understand.

For example, in the output above we saw this line:

```
Value Required RemoteIP (\d+(\.\d+){3})
```

If we put the regex portion of that line (highlighted in yellow) into regex101, it'll tell us what it translates to as per the screenshot below.

Note that it will give us a description of the regex we hover our mouse over. And, on the right hand side of the screen it will give us a description of all the regex, not just what's being hovered over.

[![regex](/assets/2016/11/regex.png)](/assets/2016/11/regex.png)

In [**my next post**](/writing-your-own-api-part-3/) I demonstrate how you can write your own templates so that you're able to obtain the information which is important to you.