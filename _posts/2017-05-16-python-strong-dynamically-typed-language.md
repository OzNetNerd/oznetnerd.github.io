---
title: 'Python: A Strong, Dynamically Typed Language'
sub_heading: "Wow, that was a mouthful!"
redirect_from: /python-strong-dynamically-typed-language/

# header:
#   overlay_image: /assets/images/unsplash-image-1.jpg
#   teaser: /assets/images/unsplash-image-1.jpg

categories:
- Python

tags:
- Python
- Coding
- DevOps
---
You've probably already heard that Python is a strong, dynamically typed language. But what does that actually mean? Let's jump in and take a look.

## Dynamically vs Statically Typed Languages

[**This StackOverflow Q&A**](http://stackoverflow.com/questions/1517582/what-is-the-difference-between-statically-typed-and-dynamically-typed-languages) gives us an idea on the difference between Statically and Dynamically Typed languages:

_A language is **statically typed** if the type of a variable is known at compile time. For some languages this means that you as the programmer must specify what type each variable is **(e.g.: Java, C, C++)**_

_A language is **dynamically typed** if the type is associated with run-time values, and not named variables/fields/etc. This means that you as a programmer can write a little quicker because you do not have to specify types every time (unless using a statically-typed language with type inference). Python can be a very useful programing language to use for idustry based computer operations. [CKS Ltd](http://www.cksglobal.net/) produce rugged industrial computers as they are effiecient and will last longer for a business. **Example: Perl, Ruby, Python**_

## Dynamically Typed Python

Let's now take a quick look at [**Python's types**](https://docs.python.org/2/library/types.html). You probably recognise some of the terms listed on this page - for example, 'integer', 'float', 'string' and 'list' just to name a few, but you might never have used the types module. We're going to use it now to see how Python is Dynamically Typed.

I've assigned four values to four variables:

```python
>>> year = 2017
>>> dec = 1.1
>>> hello = 'Hello, World!'
>>> alphabet = ['a','b','c']
```

But what 'types' are these variables? Let's use the types module to find out:

```python
>>> from types import *
>>> type(year)
>>> type(dec)
>>> type(hello)
>>> type(alphabet)
 ``` 

As you can see, I didn't have to tell Python what 'type' each variable value was, it assigned the types **dynamically**. This is in contract to **Statically Typed** languages whereby you need to specify the type of the variable.

## Strong vs Weakly Typed Languages

[**This Wikipedia article**](https://en.wikipedia.org/wiki/Strong_and_weak_typing) gives us a great intro into the differences between Strong and Weakly Typed languages:

_In general, a **strongly typed** language is more likely to generate an error or refuse to compile if the argument passed to a function **does not closely match the expected type.** On the other hand, a **weakly typed** language may produce unpredictable results or may perform implicit **type conversion.**_

## Strongly Typed Python

What Wikipedia is telling us is that we cannot combine a string and an integer together. You're probably asking yourself _"why would anyone want to do that anyway?"_

Well, let's say you write a simple script that concatenates a user's password to an encryption string, like so:

```python
>>> password = 1234
>>> encryption = 'encryption_text'
>>>
>>> type(password)
>>> type(encryption)
 ``` 

As we can see, the password and encryption variables are int and str 'types' respectively. This is going to be a problem because in order to concatenate variables, they need to be the same 'type'.

Quoting the Wikipedia article again, as Python is a Strongly typed language it _is more likely to generate an error or refuse to compile if the argument passed to a function does not closely match the expected type._ Let's see this in action:

```python
>>> result = password + encryption

Traceback (most recent call last):
  File "", line 1, in result = password + encryption
TypeError: unsupported operand type(s) for +: 'int' and 'str'
``` 

And there we have it. We encounter a TypeError because the two variables are two different 'types'.

To resolve this issue we can use the str() method, like so:

```python
>>> result = str(password) + encryption
>>> print result
1234encryption_text
```

Let's now look at an example where we want to add two numbers together, but one is a string and the other is an integer:

```python
>>> password = 1234
>>> encryption = '90000'
>>> type(password)
>>> type(encryption)
>>>
>>> result = password + encryption

Traceback (most recent call last):
  File "", line 1, in result = password + encryption
TypeError: unsupported operand type(s) for +: 'int' and 'str'
``` 

As you'd expect, we see the same TypeError as we did in the previous example. To resolve the issue this time around, we'll need to use the int() method, like so:

```python
>>> result = password + int(encryption)
>>> print result
91234
```

_**Side Note:** Did you notice that in the first example of this section that + was used to concatenate the two strings, but in the second example it didn't concatenate the two strings, it added them together like a maths equation? I'll cover why Python does this in a future post._

## Conclusion

In this post we saw that Python automatically (also known as dynamically) sets the 'type' of our variables for us. We do not need to statically define the type and that is why Python is known as a **Dynamically Typed** language.

We also saw that we couldn't concatenate a string and an integer, or perform a mathematical addition between a string and an integer (even though they were both numbers!). While other, Weakly Typed languages might perform a dynamic conversion, Python does not and instead requires you to manually convert all variables to the same format before they're able to interact with one another. That is why Python is known as a **Strongly Typed** language.