---
title: Writing your own API, Part 4
sub_heading: "Retrieve structured data from your network devices"
redirect_from: /writing-your-own-api-part-4/

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
Now that we've covered how TextFSM work and how it can be used to record useful information from device outputs, it's time to move our focus on how we use TextFSM templates in Ansible.

Using **[NTC-Ansible's](https://github.com/networktocode/ntc-ansible)** ntc_show_command module, we're able to _"get structured data from devices that don't have an API". _As I've touched on in previous posts, it does this through the use of TextFSM templates [**which can be found here**](https://github.com/networktocode/ntc-templates/tree/89c57342b47c9990f0708226fb3f268c6b8c1549/templates). I'll cover how to write your own templates in a future post but for now I'll focus on how to use the existing templates.

## Core Module

Ansible has [**Core modules**](http://docs.ansible.com/ansible/list_of_network_modules.html) which support a wide variety of network vendors and their product lines. The problem is that if the vendors' product(s) do not return the data in a structured manner then all that is returned is a block of text which you need to parse through a script.

Let's take a look at the playbook below which uses Ansible's [**ios_command**](https://docs.ansible.com/ansible/ios_command_module.html) module. (See my [**Ansible Playbook Structure**](/ansible-playbook-structure/) post for information on where I store my variables and hostnames.)

```yaml
- hosts: gns3
  gather_facts: no
  connection: local

  tasks:
  - name: DEFINE PROVIDER
	set_fact:
	  provider:
		host: "{{ inventory_hostname }}"
		username: "{{ username }}"
		password: "{{ password }}"

  - name: RUN 'show cdp neighbors'
	ios_command:
	  provider: "{{ provider }}"
	  commands:
		- show cdp neighbors
	register: neighbors

  - name: DEBUG 'show cdp neighbors'
	debug: var=neighbors
```

And now let's see what output is produced when we run this playbook:

```yaml
will@ubuntu:~/all/Ansible$ ansible-playbook -i hosts.ini show_version.yml 

PLAY [gns3] ********************************************************************

TASK [DEFINE PROVIDER] *********************************************************
ok: [10.255.0.254]

TASK [RUN 'sho cdp neighbors'] *************************************************
ok: [10.255.0.254]

TASK [DEBUG 'show cdp neighbors'] **********************************************
ok: [10.255.0.254] => {
    "neighbors": {
        "changed": false, 
        "stdout": [
            "Capability Codes: R - Router, T - Trans Bridge, B - Source Route Bridge
            S - Switch, H - Host, I - IGMP, r - Repeater
            Device ID        Local Intrfce     Holdtme    Capability  Platform  Port ID
            R3.lab           Fas 1/0            162         R S I     3745      Fas 0/1
            R2.lab           Fas 0/1            162         R S I     3745      Fas 0/0"
        ], 
        "stdout_lines": [
            [
                "Capability Codes: R - Router, T - Trans Bridge, B - Source Route Bridge", 
                "                  S - Switch, H - Host, I - IGMP, r - Repeater", 
                "", 
                "Device ID        Local Intrfce     Holdtme    Capability  Platform  Port ID", 
                "R3.lab           Fas 1/0            162         R S I     3745      Fas 0/1", 
                "R2.lab           Fas 0/1            162         R S I     3745      Fas 0/0"
            ]
        ], 
        "warnings": []
    }
}

PLAY RECAP *********************************************************************
10.255.0.254               : ok=3    changed=0    unreachable=0    failed=0
```

As mentioned above, the problem is that the important information (e.g the neighbor's name, local interface and remote interface) is output as a block of text. If we wanted to use this information elsewhere in our playbook (e.g to update the router's interface descriptions to contain the neighbor's hostname), we'd need to write a script to extract the information and store it as a variable which would be quite time consuming. Thankfully NTC-Ansible solves this issue.

## ntc_show_command Module

Let's now modify the above playbook so that instead of using the ios_command module, we use the ntc_show_command module which will in turn use the [**cisco_ios_show_cdp_neighbors**](https://github.com/networktocode/ntc-templates/blob/89c57342b47c9990f0708226fb3f268c6b8c1549/templates/cisco_ios_show_cdp_neighbors.template) TextFSM template:

```yaml
- hosts: gns3
  gather_facts: no
  connection: local

  tasks:
  - name: NTC_SHOW 'show cdp neighbors'
    ntc_show_command:
      connection: ssh
      platform: cisco_ios
      command: "show cdp neighbors"
      host: "{{ inventory_hostname }}"
      username: "{{ username }}"
      password: "{{ password }}"
    register: ntc_neighbors

  - name: DEBUG NTC_SHOW 'show cdp neighbors'
    debug: var=ntc_neighbors
```

And now let's see what output is produced when we run this playbook:

```yaml
will@ubuntu:~/all/Ansible$ ansible-playbook -i hosts.ini show_version.yml 

PLAY [gns3] ********************************************************************

TASK [NTC_SHOW 'show cdp neighbors'] *******************************************
ok: [10.255.0.254]

TASK [DEBUG NTC_SHOW 'show cdp neighbors'] *************************************
ok: [10.255.0.254] => {
    "ntc_neighbors": {
        "changed": false, 
        "response": [
            {
                "local_interface": "Fas 1/0", 
                "neighbor": "R3.lab", 
                "neighbor_interface": "Fas 0/1"
            }, 
            {
                "local_interface": "Fas 0/1", 
                "neighbor": "R2.lab", 
                "neighbor_interface": "Fas 0/0"
            }
        ], 
        "response_list": []
    }
}

PLAY RECAP *********************************************************************
10.255.0.254               : ok=2    changed=0    unreachable=0    failed=0
```

Here we see that ntc_show_command has output the important pieces of information in a structured fashion so that we can now use them as variables in our playbook.

As a basic example, let's add the following lines to the bottom of our playbook:

```yaml
  - name: Display debug message
    debug: msg="Devices {{ ntc_neighbors.response[0].neighbor }} and {{ ntc_neighbors.response[1].neighbor }} are connected to {{ inventory_hostname }}"
```

_Don't worry if you find the_ ntc_neighbors.response[0].neighbor  _and_ ntc_neighbors.response[1].neighbor  _variables confusing, I will cover them in a future post._

And now let's rerun our playbook:

```yaml
will@ubuntu:~/all/Ansible$ ansible-playbook -i hosts.ini show_version.yml 

PLAY [gns3] ********************************************************************

TASK [NTC_SHOW 'show cdp neighbors'] *******************************************
ok: [10.255.0.254]

TASK [DEBUG NTC_SHOW 'show cdp neighbors'] *************************************
ok: [10.255.0.254] => {
    "ntc_neighbors": {
        "changed": false, 
        "response": [
            {
                "local_interface": "Fas 1/0", 
                "neighbor": "R3.lab", 
                "neighbor_interface": "Fas 0/1"
            }, 
            {
                "local_interface": "Fas 0/1", 
                "neighbor": "R2.lab", 
                "neighbor_interface": "Fas 0/0"
            }
        ], 
        "response_list": []
    }
}

TASK [Display debug message] ***************************************************
ok: [10.255.0.254] => {
    "msg": "Devices R3.lab and R2.lab are connected to 10.255.0.254"
}

PLAY RECAP *********************************************************************
10.255.0.254               : ok=3    changed=0    unreachable=0    failed=0
```

While this debug message isn't particularly useful and a loop should have really ben used to extract the data, I used this basic example as it is the simplest way to demonstrate the power of the ntc_show_command module. I will provide more useful examples in future posts, but in the meantime please take a look at **[Gabriele's fantastic post](https://projectme10.wordpress.com/2015/11/03/enabling-network-automation-using-ntc-ansible/)** on using the module in a real life situation.