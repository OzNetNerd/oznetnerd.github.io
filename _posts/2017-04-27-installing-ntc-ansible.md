---
title: Installing NTC Ansible
sub_heading: "A step-by-step guide"
redirect_from: /installing-ntc-ansible/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Ansible
- TextFSM

tags:
- Ansible
- TextFSM
- DevOps
---
[**I've written about NTC-Ansible**](/?s=ntc-ansible) a couple of times already and thought now would be a good time to run you through the installation process. The **[github page](https://github.com/networktocode/ntc-ansible)** provides some great information on the process, but I'd like to add a few extra details in this post.

Note that there are two ways in which you can install custom modules. They work as well as one another so it's up to you which you choose.

## Common steps

Both methods require the following tasks be completed:

1.  Clone the ntc-ansible repository:
	  ```bash
	  git clone https://github.com/networktocode/ntc-ansible --recursive
	  ```

2.  Install pip if it hasn't already been installed:

	  ```bash
	  sudo apt install python-pip
	  ```

3.  Install the ntc-ansible dependencies:

	  ```bash
	  pip install ntc-ansible
	  ```

## Method #1 - Configuration directory

Move the filter_plugins , `library` and `ntc-templates` directories so that they're in the same location as your Playbooks. For example, if your directory structure currently looks like this:

```
├── site.yml
├── group_vars
│   ├── all.yml
├── hosts.ini
├── roles
    ├── common
    └── gns3
        ├── tasks
            ├── main.yml
```

It should look like this after the directory moves:

```
├── filter_plugins
│   ├── ├── library
│   ├── ├── ntc-templates
│   ├── 
├── site.yml
├── group_vars
│   ├── all.yml
├── hosts.ini
├── roles
    ├── common
    └── gns3
        ├── tasks
            ├── main.yml
```

_**Note:** See my [**"Anatomy of an Ansible Playbook"**](/anatomy-ansible-playbook/) post for more information on Ansible directory structures._

Once you've moved the directories across, you're ready to start using the NTC Ansible modules.

## Method #2 - Modules directory

1.  Move the filter_plugins  and ntc-templates  so that they're in the same location as your Playbooks (see **Method #1** for more information). **Do not** move the library  directory to this location.
2.  By default, Ansible's module search path  is /usr/share/my_modules/ .  You can check if this has been overridden by using the following command:

	```
	will@ubuntu:/tmp$ ansible --version
	ansible 2.2.2.0
	config file = /etc/ansible/ansible.cfg
	configured module search path = Default w/o overrides
	```

3.  Create an ntc-ansible  directory in the module search path directory.
4.  Move the NTC Ansible library  directory into the ntc-ansible  directory you've just created.

Your directory structure should now look like this:

```
will@ubuntu:/usr/share/my_modules/ntc-ansible
$ ls
library
```

You're ready to start using the NTC Ansible modules.

**Note:** If this process does not work and you've got the configured module search path  set to default, edit its library  path in /etc/ansible/ansible.cfg  and point it to the directory parent directory of ntc-ansible . For example:

```
will@ubuntu:/tmp$ ansible --version
ansible 2.2.2.0
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/usr/share/my_modules/']
```