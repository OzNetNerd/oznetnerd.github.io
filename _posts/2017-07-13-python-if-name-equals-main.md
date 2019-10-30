---
title: 'Python: if __name__ == "__main__"'
sub_heading: "That's a lot of underscores!"
redirect_from: /python-if-name-equals-main/

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
There are plenty of articles on the internet that attempt to explain what if __name__ == "__main__" is and what it does, but (in my humble opinion), the examples are too complex more often than not. With that in mind, this post is aimed at being the most simplest explanation on the planet! :)

What does it do?
----------------

This statement is used when you want your code to be used as both a standalone script, as well as a module that can be imported and used by other scripts.

For example, if it is run as a standalone script, you may want to provide a menu to ensure users input all of the necessary information. On the other hand, if it is being imported as a module, perhaps you'd like to avoid the menu all together and instead only use the functions contained the script (e.g func1 in the example below).

To achieve the above outcome, you would use the following code:

```python
if __name__ == "__main__":
    

def func1():
``` 

## How does it work?


Although `__name__`  is sometimes referred to as a "magic method", it really isn't all that exciting. As we'll see in a moment, it does one of two things:

1.  If it is run against an imported module, it will return the name of the file which contains the module's code.
2.  If it is run against a script that is not being imported, it will return __main__.

Let's now look at two files, `my_module.py` and `main_test.py`:

`my_module.py`:

```python
def print_name():
    return "This is my_module.py's output: {}".format(__name__)

if __name__ == '__main__':
    print 'Menu - Please provide all required information'
    print print_name()
```

`main_test.py`:

```python
import my_module

print "I don't want a menu, just the name of the module!"
print my_module.print_name()
```

Running the `my_module.py` file results in the following output:

```
Menu - Please provide all required information
This is my_module.py's output: __main__
```

Running the `main_test.py` file results int he following output:

```
I don't want a menu, just the name of the module!
This is my_module.py's output: my_module
```