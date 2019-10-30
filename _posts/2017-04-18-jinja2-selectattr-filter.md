---
title: Jinja2 selectattr() Filter
sub_heading: "Filtering with selectattr()"
redirect_from: /jinja2-selectattr-filter/

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
As per [**the documentation**](http://jinja.pocoo.org/docs/2.9/templates/#map):

_"selectattr() filters a sequence of objects by applying a **test** to the **specified attribute** of each object, and only selecting the objects with the test succeeding. __If no test is specified, the attribute’s value will be evaluated as a boolean.__"_

## Example #1

**[This blog post](http://blog.halberom.co.uk/post/ansible-selectattr/)** does a great job of demonstrating how to use selectattr( ) . Let's take a look at how it works.

First, the author creates a **"users"** dictionary:

```yaml
{% raw %}---{% endraw %}
users:
  - name: john
    email: john@example.com
  - name: jane
    email: jane@example.com
  - name: fred
    email: fred@example.com
    password: 123!abc
```

Next, the author runs the following:

```yaml
- set_fact:
    {% raw %}emails: "{{ users | selectattr('password', 'undefined') | map(attribute='email') | list }}"{% endraw %}
```

What we see here is:

1.  the **"users"** dictionary is being passed to the selectattr( )  filter.
2.  The selectattr() filter will cycle through all of the dictionaries (also known as objects) inside of **"users"**, looking for which ones do and do not have the **"password"** [**attribute**](/tags/#jinja2) specified.
3.  Because the [**'undefined'** **Test**](http://jinja.pocoo.org/docs/2.9/templates/#undefined) has been passed as a parameter, only the objects which do not have a **"password"** attribute defined will be recorded.
4.  The **[map( )](/jinja2-map-filter/) **filter is then used to return the **"email"** attribute of all objects that were recorded in the previous step.

## Example #2

This **[StackOverflow](http://stackoverflow.com/questions/31895602/ansible-filter-a-list-by-its-attributes)** response also has some great examples of how selectattr( )  can be used with both Jinja ([equalto( )](http://jinja.pocoo.org/docs/2.9/templates/#equalto) ) and Ansible ([match( )](https://docs.ansible.com/ansible/playbooks_tests.html#testing-strings)  and [search( )](https://docs.ansible.com/ansible/playbooks_tests.html#testing-strings) ) tests.

Using this dictionary:

```yaml
- hosts: localhost
  connection: local
  gather_facts: no
  vars:
    network:
      addresses:
        private_ext:
          - type: fixed
            addr: 172.16.2.100
        private_man:
          - type: fixed
            addr: 172.16.1.100
          - type: floating
            addr: 10.90.80.10
```

We can obtain the **"floating"** value using any one of following filters:

```yaml
- {% raw %}debug: msg={{ network.addresses.private_man | selectattr("type", "equalto", "floating") | map(attribute='addr') | list }}{% endraw %}
- {% raw %}debug: msg={{ network.addresses.private_man | selectattr("type", "match", "^floating$") | map(attribute='addr') | list }}{% endraw %}
- {% raw %}debug: msg={{ network.addresses.private_man | selectattr("type", "search", "^floating$") | map(attribute='addr') | list }}{% endraw %}
```

The above lines result in the following output:

```yaml
TASK [debug] *******************************************************************
ok: [localhost] => {
    "msg": [
        "10.90.80.10"
    ]
}

TASK [debug] *******************************************************************
ok: [localhost] => {
    "msg": [
        "10.90.80.10"
    ]
}

TASK [debug] *******************************************************************
ok: [localhost] => {
    "msg": [
        "10.90.80.10"
    ]
}
```