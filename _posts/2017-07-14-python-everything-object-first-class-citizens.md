---
title: 'Python: Everything is an Object & First Class Citizens'
sub_heading: "All things being equal..."
redirect_from: /python-everything-object-first-class-citizens/

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
A lot of Python books often mention that _"everything in Python is an object"_, and _"objects are first class citizens"_, but they don't always explain what that these things actually mean. Let's try to fix that up now.

## Everything in Python is an Object

[**Dive Into Python**](http://www.diveintopython.net/getting_to_know_python/everything_is_an_object.html) gives a great explanation:

_Different programming languages define “object” in different ways. In some, it means that all objects must have attributes and methods; in others, it means that all objects are subclassable. In Python, the definition is looser; some objects have neither attributes nor methods (more on this in Chapter 3), and not all objects are subclassable (more on this in Chapter 5). But **everything is an object in the sense that it can be assigned to a variable or passed as an argument to a function** (more in this in Chapter 4)._

_This is so important that I'm going to repeat it in case you missed it the first few times: everything in Python is an object. **Strings are objects. Lists are objects. Functions are objects. Even modules are objects.**_

For more information on objects, see [**this effbot post**](http://www.effbot.org/zone/python-objects.htm).

## First Class Citizens

For this definition I couldn't go past [**Guido van Rossum's blog post**](http://python-history.blogspot.com.au/2009/02/first-class-everything.html). After all, he's the one who wrote Python so I'm sure he knows what he's talking about :)

_One of my goals for Python was to make it so that all objects were "first class." By this, I meant that I wanted **all objects** that could be named in the language (e.g., integers, strings, functions, classes, modules, methods, etc.) to have **equal status.** That is, they can be **assigned to variables, placed in lists, stored in dictionaries, passed as arguments, and so forth.**_