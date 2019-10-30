---
title: Jinja2 default() Test
sub_heading: "Let's take default() for a test drive"
redirect_from: /jinja2-default-test/

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
## Example #1

**Reference: [Jinja](http://jinja.pocoo.org/docs/2.9/templates/#default)**

Syntax: 

```
default(value, default_value=u'', boolean=False)
```

## Example #1

If the value is undefined it will return the passed default value, otherwise the value of the variable:

```
{% raw %}{{ my_variable|default('my_variable is not defined') }}{% endraw %}
```

This will output the value of my_variable  if the variable was defined, otherwise **'my_variable is not defined'**. If you want to use default with variables that evaluate to false  you have to set the second parameter to true:

```
{% raw %}{{ ''|default('the string was empty', true) }}{% endraw %}
```

## Example #2

**Reference: [webforefront](https://www.webforefront.com/django/usebuiltinjinjafilters.html)**

The default  or d  filter is used to specify a default value if a variable is undefined or is false (i.e. it doesn't exist or is empty). For example, the filter statement:

```
{% raw %}{{variable|default("no value")}}{% endraw %}
```

outputs **"no value"** only if the variable is undefined, otherwise it outputs the variable value.

## Example #3

If in addition you want to provide a default value for a variable that evaluates to false , is None  or is an empty string, you have to add True  as a second filter parameter, e.g:

```
{% raw %}{{variable|default("no value",true)}}{% endraw %}
```

outputs "no value" if the variable is undefined, false , is None  or is an empty string).