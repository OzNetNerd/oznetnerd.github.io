---
title: 'Python: Shadowing'
sub_heading: "Don't do it!"
redirect_from: /python-shadowing/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Python
- DevOps

tags:
- Python
- Coding
- DevOps
---
In my previous post, [**Python: Scope**](/python-scope/), I touched on the topic of Shadowing. In this post I'll be delving deeper into it.

As [**Wikipedia says**](https://en.wikipedia.org/wiki/Variable_shadowing), _variable shadowing occurs when a variable declared within a certain scope (decision block, method, or inner class) has the same name as a variable declared in an outer scope._

There are some interesting debates on whether shadowing is a bad thing or not [**in this StackOverflow Q&A**](https://stackoverflow.com/questions/19902127/what-is-the-pythonic-way-to-avoid-shadowing-variables) as well as **[this one](https://stackoverflow.com/questions/20125172/how-bad-is-shadowing-names-defined-in-outer-scopes)**. In a nutshell, there are three trains of thought:

1.  It's fine to use shadowing.
2.  You should avoid shadowing by ensuring all names are unique.
3.  You should avoid shadowing by using functions.

Let's now run through each of these options to see how they work.

## It's fine to use shadowing

As we saw in the [**Python: Scope**](/python-scope/) post, shadowing allows us to use the same name to map to multiple objects if each mapping is done in a different scope. For example:

```python
# global
name = 'tom'

def hello_name(name):
    # local
    print 'hello {}'.format(name)

print name
hello_name('john')

# Output

tom # global
hello john # local
```

OK great, everything works as expected. Both our **global** and **local** mappings are kept intact. However, what happens if we change the name parameter to person in the definition, but not the body of the function:

```python
# global
name = 'tom'

def hello_name(person):
    # local
    print 'hello {}'.format(name)

print name
hello_name('john')

# Output

tom
hello tom
```

Because of [**Pyhon's LEGB rule**](/python-scope/), instead of raising an exception Python uses the **global** mapping for name because it can't find one **locally**, and therefore we might be unaware of our mistake. It is for this reason that people suggest avoiding shadowing.

## Avoid shadowing by ensuring all names are unique

As suggested in [**PEP8**](https://www.python.org/dev/peps/pep-0008/#descriptive-naming-styles), you can append a trailing underscore (_) to a name in order to ensure it is unique:

```python
# global
name = 'tom'

def hello_name(name_):
    # local
    print 'hello {}'.format(name_)

print name
hello_name('john')
```

This time if we change the name_ parameter to person_ in the definition, but not the body of the function an exception is raised: NameError: global name 'name_' is not defined. This makes it obvious that there is in an error in our code which needs to be fixed.

## Avoid shadowing by using functions

This method tackles the issue in a different way. While the previous two options were related to name mappings that had scopes in the same hierarchy, this method uses parallel scopes and therefore avoids the issue we saw in the first option all together.

Let's take a look at an example:

```python
def main():
    name = 'tom'
    print 'hello {}'.format(name)

def hello_name(name):
    print 'hello {}'.format(name)

main()
hello_name('john')

# Output

hello tom # main()
hello john # hello_name(name)
```

Because we've removed the **global** name variable and put it into a function instead, we've completely eliminated the shadowing between the **global** scope and the hello_name(name) **local** scope. This means that if we change the hello_name parameter in the definition but not the body of the function, we'll see an exception the same way as we did in the second example.