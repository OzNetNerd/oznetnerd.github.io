---
title: Jinja2 Tests Overview
sub_heading: "What are tests anyway?"
redirect_from: /jinja2-tests-overview/

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
As per the [**Jinja documentation**](http://jinja.pocoo.org/docs/2.9/templates/):

_Beside [**"filters****"**](/tags/#jinja2), there are also so-called **“tests”** available. Tests can be used to test a variable against a common expression._

Filters such as [selectattr()](/jinja2-selectattr-filter/) can accept **tests **as a parameter. As per [**the documentation**](http://jinja.pocoo.org/docs/2.9/templates/#map):

_"selectattr() filters a sequence of objects by applying a [**test**](/tags/#jinja2) to the **specified [attribute](/tags/#jinja2)** of each object, and only selecting the objects with the test succeeding. __If no test is specified, the attribute’s value will be evaluated as a boolean.__"_

We seen an example of this on the [selectattr () **page**](/jinja2-selectattr-filter/) where the undefined **Test** is passed as a parameter:

```yaml
- set_fact:
    {% raw %}emails: "{{ users | selectattr('password', 'undefined') | map(attribute='email') | list }}"{% endraw %}
```

Jinja & Ansible Tests
---------------------

While Jinja provides numerous tests, Ansible brings some of its own to the table too. You can find their documentation here:

*   [**Jinja Tests**](http://jinja.pocoo.org/docs/dev/templates/#list-of-builtin-tests)
*   **[Ansible Tests](https://docs.ansible.com/ansible/playbooks_tests.html)**