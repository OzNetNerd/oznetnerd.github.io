---
title: Jinja2 Filter Overview
sub_heading: "What are Jinja2 filters anyway?"
redirect_from: /jinja2-filter-overview/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Ansible

tags:
- Ansible
- Automation
- Jinja2
- DevOps
---
**Reference: [Stack Overflow](http://stackoverflow.com/questions/30883151/ansible-jinja2-filters-pipe-what-does-it-mean)**

With the pipe character you pass a value to a filter. There are numerous **[Jinja 2 filters](http://jinja.pocoo.org/docs/dev/templates/#builtin-filters)** but Ansible **[brings some additional filters](http://docs.ansible.com/playbooks_filters.html)**. _(Also see [**Conditionals**](http://docs.ansible.com/ansible/playbooks_conditionals.html).)_

The term _filter_ might be confusing at times because all the filters work very differently. Some for example reduce a result set of a hash/array, some modify contents of a string, but then there are filters which simply return true or false.

A better explanation might be that those are modifiers and they can do anything with your passed data. You can even **[write your own filters](http://docs.ansible.com/developing_plugins.html#filter-plugins)**.

Filters can be chained, passing the result from the first filter to the next and so forth. It works exactly like piping commands on a unix shell.

```
"value" | filter1 | filter2 | filterN
```

The failed  filter returns true  if the passed result has failed. It simply checks the failed  property from result .

The changed  filter is the same, but checks if the passed result has changes. It checks the changed property from result .

`ipaddr | replace(",", ".")`  replaces all occurrences of `,`  with `.` so a value of 127,0,0,1  will be transformed to 127.0.0.1 .

The default  filter will set a default value if the input was null, e.g. an undefined variable. undefined_var | default("var was undefined")  -> This will either print the contents of undefined_var  or the string "var was undefined" .

## Additional Overview Information

**Reference: [Jinja](http://jinja.pocoo.org/docs/dev/templates/#filters)**

Variables can be modified by **filters**. Filters are separated from the variable by a pipe symbol (| ) and may have optional arguments in parentheses. Multiple filters can be chained. The output of one filter is applied to the next.

For example, `{{ name|striptags|title }}` will remove all HTML Tags from variable _name_ and title-case the output (title(striptags(name))) .

Filters that accept arguments have parentheses around the arguments, just like a function call. For example: `{% raw %}{{ listx|join(', ') }}{% endraw %}` will join a list with commas `(str.join(', ', listx))`.

The [**List of Builtin Filters**](http://jinja.pocoo.org/docs/dev/templates/#builtin-filters) describes all the builtin filters.

## Nested Filters

**Reference: [OzNetNerd.com](oznetnerd.com)**

Filters such as [map( )](/jinja2-map-filter/)  can accept another **filter** as a parameter. As per [**the documentation**](http://jinja.pocoo.org/docs/2.9/templates/#map):

_"map( )  applies a **filter** on a sequence of objects or looks up an attribute."_

We seen an example of this on the [map ( )  page](/jinja2-map-filter/) where we used the [upper( )](http://jinja.pocoo.org/docs/2.9/templates/#upper)  filter as a parameter:

```
{% raw %}{{ names | map('upper') | list }}{% endraw %}
```

## Attributes

**Reference: [OzNetNerd.com](oznetnerd.com)**

Filters such as [map(  )](/jinja2-map-filter/)  can accept **attributes** as a parameter. As per [**the documentation**](http://jinja.pocoo.org/docs/2.9/templates/#map):

_"map( )  applies a filter on a sequence of objects or looks up an **attribute**."_

We seen an example of this on the [map ( )  page](/jinja2-map-filter/) where we used the first and last   attribute as a parameters:

```
{% raw %}{{ names | map(attribute='first') | list }}{% endraw %}
{% raw %}{{ names | map(attribute='last') | list }}{% endraw %}
```

## Ansible Filter Source Code

At the time of writing, regex_search  and regex_findall  **[filters](/tags/#jinja2) **(discussed [**here**](https://github.com/ansible/ansible/pull/14696)) have been integrated into Ansible but not yet documented. Having said this, Ansible has been notified so the issue should soon be resolved.

If you come across a similar situation in the future, after [**notifying Ansible**](https://github.com/ansible/ansible/issues/new) you can [**browse the source code**](https://github.com/ansible/ansible/tree/stable-2.2/lib/ansible/plugins/filter) and figure out how the filter works yourself. Note that you'll need to ensure you select the correct code branch for the version of Ansible you're running.

## Tests

**Reference: [OzNetNerd.com](oznetnerd.com)**

As per the [**Jinja documentation**](http://jinja.pocoo.org/docs/2.9/templates/):

_Beside filters, there are also so-called [**“tests”**](/tags/#jinja2) available. Tests can be used to test a variable against a common expression._

Filters such as [selectattr( )](/jinja2-selectattr-filter/)   can accept **tests **as a parameter. As per [**the documentation**](http://jinja.pocoo.org/docs/2.9/templates/#map):

_"selectattr( ) filters a sequence of objects by applying a [**test**](/jinja2-filter-overview/) to the **specified [attribute](/jinja2-filter-overview/)** of each object, and only selecting the objects with the test succeeding. __If no test is specified, the attribute’s value will be evaluated as a boolean.__"_

We seen an example of this on the [selectattr ( )  **page**](/tags/#jinja2) where the undefined  **[Test](http://jinja.pocoo.org/docs/2.9/templates/#undefined)** is passed as a parameter:

```
- set_fact:
    {% raw %}emails: "{{ users | selectattr('password', 'undefined') | map(attribute='email') | list }}"{% endraw %}
```