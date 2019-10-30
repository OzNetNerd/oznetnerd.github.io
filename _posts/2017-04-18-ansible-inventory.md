---
title: Ansible Inventory
sub_heading: "Working with Ansible's inventory"
redirect_from: /ansible-inventory/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Ansible

tags:
- Ansible
- Automation
- DevOps
---
## Hostnames

**Reference: Ansible [Variables](http://docs.ansible.com/ansible/playbooks_variables.html) & [Inventory ](http://docs.ansible.com/ansible/intro_inventory.html)**

inventory_hostname  is the name of the hostname as configured in Ansible’s inventory host file. This can be useful for when you don’t want to rely on the discovered hostname ansible_hostname  or for other mysterious reasons. If you have a long FQDN, inventory_hostname_short  also contains the part up to the first period, without the rest of the domain.

Suppose you have just static IPs and want to set up some aliases that live in your host file, or you are connecting through tunnels. You can also describe hosts like this:

```
jumper ansible_port=5555 ansible_host=192.0.2.50
```

In the above example, trying to run Ansible against the host alias “jumper” (which may not even be a real hostname) will contact 192.0.2.50 on port 5555.

Note that these variables are used in Tasks. For example:

```yaml
  - name: ntc_show - show CDP neighbors
    ntc_show_command:
      connection: ssh
      platform: cisco_ios
      command: "show cdp neighbor"
      host: "{{ ansible_host }}"
      username: "{{ username }}"
      password: "{{ password }}"
    register: results
```

## Further Information

**Reference: [Ansible](http://docs.ansible.com/ansible/intro_inventory.html)**

As alluded to above, setting the following variables controls how Ansible interacts with remote hosts.

**Host connection:**

**ansible_connection:** Connection type to the host. This can be the name of any of ansible’s connection plugins. SSH protocol types are smart , ssh  or paramiko . The default is smart . Non-SSH based types are described in the next section.

**SSH connection:**

**ansible_host:** The name of the host to connect to, if different from the alias you wish to give to it.

**ansible_port:** The ssh port number, if not 22

**ansible_user:** The default ssh user name to use.

**ansible_ssh_pass:** The ssh password to use (never store this variable in plain text; always use a vault. See Variables and Vaults)

**ansible_ssh_private_key_file:** Private key file used by ssh. Useful if using multiple keys and you don’t want to use SSH agent.

**ansible_ssh_common_args:** This setting is always appended to the default command line for sftp, scp, and ssh. Useful to configure a ProxyCommand for a certain host (or group).

**ansible_sftp_extra_args:** This setting is always appended to the default sftp command line.

**ansible_scp_extra_args:** This setting is always appended to the default scp command line.

**ansible_ssh_extra_args:** This setting is always appended to the default ssh command line.

**ansible_ssh_pipelining:** Determines whether or not to use SSH pipelining. This can override the pipelining setting in ansible.cfg.

See the [**Ansible Inventory**](http://docs.ansible.com/ansible/intro_inventory.html) page for more options.