---
title: Ansible Variables
sub_heading: "Learning about Ansible variables"
redirect_from: /ansible-variables/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Ansible
- Jinja2

tags:
- Ansible
- Automation
- Jinja2
- DevOps
---
## Using Variables: About Jinja2

**Reference: [Ansible](http://docs.ansible.com/ansible/playbooks_variables.html)**

Ansible allows you to reference variables in your playbooks using the Jinja2 templating system. While you can do a lot of complex things in Jinja, only the basics are things you really need to learn at first.

For instance, in a simple template, you can do something like:

```
My amp goes to {% raw %}{{ max_amp_value }}{% endraw %}
```

And that will provide the most basic form of variable substitution.

This is also valid directly in playbooks, and you’ll occasionally want to do things like:

```
{% raw %}template: src=foo.cfg.j2 dest={{ remote_install_path }}/foo.cfg{% endraw %}
```

In the above example, we used a variable to help decide where to place a file.

Inside a template you automatically have access to all of the variables that are in scope for a host. Actually it’s more than that – you can also read variables about other hosts. We’ll show how to do that in a bit.

## Registered Variables

*   **Reference: [Ansible](http://docs.ansible.com/ansible/playbooks_variables.html)**

Another major use of variables is running a command and using the result of that command to save the result into a variable. Results will vary from module to module. Use of -v  when executing playbooks will show possible values for the results.

The value of a task being executed in ansible can be saved in a variable and used later. See some examples of this in the **[Conditionals](http://docs.ansible.com/ansible/playbooks_conditionals.html)** chapter.

While it’s mentioned elsewhere in that document too, here’s a quick syntax example:

```yaml
- hosts: web_servers

  tasks:

     - shell: /usr/bin/foo
       register: foo_result
       ignore_errors: True

     - shell: /usr/bin/bar
       when: foo_result.rc == 5
```

Registered variables are valid on the host the remainder of the playbook run, which is the same as the lifetime of [**“facts”**](/ansible-set_facts/) in Ansible. Effectively registered variables are just like facts.

When using register with a loop the data structure placed in the variable during a loop, will contain a results  attribute, that is a list of all responses from the module. For a more in-depth example of how this works, see the **[Loops](http://docs.ansible.com/ansible/playbooks_loops.html)** section on using register with a loop.

## Accessing Complex Variable Data

*   **Reference: [Ansible](http://docs.ansible.com/ansible/playbooks_variables.html)**

Some provided facts, like networking information, are made available as nested data structures. To access them a simple {{ foo }}  is not sufficient, but it is still easy to do. Here’s how we get an IP address:

```
{% raw %}{{ ansible_eth0["ipv4"]["address"] }}{% endraw %}
```

OR alternatively:

```
{% raw %}{{ ansible_eth0.ipv4.address }}{% endraw %}
```

Similarly, this is how we access the first element of an array:

```
{% raw %}{{ foo[0] }}{% endraw %}
```

## Passing Variables On The Command Line

*   **Reference: [Ansible](http://docs.ansible.com/ansible/playbooks_variables.html)**

In addition to vars_prompt  and vars_files , it is possible to send variables over the Ansible command line. This is particularly useful when writing a generic release playbook where you may want to pass in the version of the application to deploy:

```
ansible-playbook release.yml --extra-vars "version=1.23.45 other_variable=foo"
```

This is useful, for, among other things, setting the hosts group or the user for the playbook.

Example:

```yaml
{% raw %}---{% endraw %}

- hosts: '{{ hosts }}'
  {% raw %}remote_user: '{{ user }}'{% endraw %}

  tasks:
     - ...

ansible-playbook release.yml --extra-vars "hosts=vipers user=starbuck"
```

As of Ansible 1.2, you can also pass in extra vars as quoted JSON, like so:

```
--extra-vars '{"pacman":"mrs","ghosts":["inky","pinky","clyde","sue"]}'
```

The `key=value` form is obviously simpler, but it’s there if you need it!

As of Ansible 1.3, extra vars can be loaded from a JSON file with the @  syntax:

```
--extra-vars "@some_file.json"
```

Also as of Ansible 1.3, extra vars can be formatted as YAML, either on the command line or in a file as above.

## Variable Precedence

*   **Reference: [Ansible](http://docs.ansible.com/ansible/playbooks_variables.html)**

In descending order:

*   role defaults
*   inventory INI or script group vars
*   inventory group_vars/all
*   playbook group_vars/all
*   inventory group_vars/*
*   playbook group_vars/*
*   inventory INI or script host vars
*   inventory host_vars/*
*   playbook host_vars/*
*   host facts
*   play vars
*   play vars_prompt
*   play vars_files
*   role vars (defined in role/vars/main.yml)
*   block vars (only for tasks in block)
*   task vars (only for the task)
*   role (and include_role) params
*   include params
*   include_vars
*   set_facts / registered vars
*   extra vars (always win precedence)

Basically, anything that goes into “role defaults” (the defaults folder inside the role) is the most malleable and easily overridden. Anything in the vars directory of the role overrides previous versions of that variable in namespace. The idea here to follow is that the more explicit you get in scope, the more precedence it takes with command line -e  extra vars always winning. Host and/or inventory variables can win over role defaults, but not explicit includes like the vars directory or an include_vars  task.

## Variable Scopes

*   **Reference: [Ansible](http://docs.ansible.com/ansible/playbooks_variables.html)**

Ansible has 3 main scopes:

*   **Global:** this is set by config, environment variables and the command line
*   **Play:** each play and contained structures, vars entries, include_vars, role defaults and vars.
*   **Host:** variables directly associated to a host, like inventory, facts or registered task outputs