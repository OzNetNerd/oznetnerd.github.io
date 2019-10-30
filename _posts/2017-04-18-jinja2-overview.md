---
title: Jinja2 Overview
sub_heading: "Let's have a look at Jinja2 templating"
redirect_from: /jinja2-overview/

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
There are two ways in which you can use Jinja templates inside of Ansible are:

*   [**Templates**](/tags/#jinja2)
*   [**Filters**](/tags/#jinja2)

[**This blog post**](http://blog.halberom.co.uk/post/ansible-selectattr/) demonstrates both of these techniques being used. The information contained in the post is contained below for convenience.

## Data

The following data will be used by the template and filter in the subsequent sections of this page:

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

## Templates

Templates require a template file which contains the smarts of what you're wanting to do:

```
{% raw %}# get_emails.j2
[
{% for user in users %}
  {% if user.password is undefined %}
{{ user.email }},
  {% raw %}{% endif %}
{% endfor %}
]{% endraw %}
```

You can then call the template from inside of Ansible using a [**lookup**](http://docs.ansible.com/ansible/playbooks_lookups.html):

```yaml
- set_fact:
    {% raw %}emails: "{{ lookup('template', './get_emails.j2' }}"{% endraw %}
```

## Filters

In this case, a filter can achieve the same result as the template:

```yaml
- set_fact:
    {% raw %}emails: "{{ users | selectattr('password', 'undefined') | map(attribute='email') | list }}"{% endraw %}
```