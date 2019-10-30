---
title: Ansible Lists & Dicts Overview
sub_heading: "It's time to take it up a notch"
redirect_from: /ansible-lists-dicts-overview/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Ansible

tags:
- Ansible
- Automation
- DevOps
- Jinja2
---
**Reference: [Ansible](http://docs.ansible.com/ansible/YAMLSyntax.html)**

All members of a **list** are lines beginning at the same indentation level starting with a `-  `(a dash and a space):

```yaml
{% raw %}---{% endraw %}
# A list of tasty fruits
fruits:
    - Apple
    - Orange
    - Strawberry
    - Mango
```

A **dictionary** is represented in a simple key: value  form (the colon must be followed by a space):

```yaml
# An employee record
martin:
    name: Martin D'vloper
    job: Developer
    skill: Elite
```

More complicated data structures are possible, such as lists of dictionaries, dictionaries whose values are lists or a mix of both:

```yaml
# Employee records
-  martin:
    name: Martin D'vloper
    job: Developer
    skills:
      - python
      - perl
      - pascal
-  tabitha:
    name: Tabitha Bitumen
    job: Developer
    skills:
      - lisp
      - fortran
      - erlang
```

Dictionaries and lists can also be represented in an abbreviated form if you really want to:

```yaml
{% raw %}---{% endraw %}
martin: {name: Martin D'vloper, job: Developer, skill: Elite}
fruits: ['Apple', 'Orange', 'Strawberry', 'Mango']
```

## Additional Example

Reference: [**OzNetNerd.com**](oznetnerd.com)

The below example can was taken from the [selectattr( )  **page**](/jinja2-selectattr-filter/):

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