---
title: 'Python: Scope'
sub_heading: "Local, enclosing functions global and built-in"
redirect_from: /python-scope/

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
Scope is the term used to define the location(s) in which Python searches for a name to object mapping (e.g a variable).

As described [**in this StackOverflow post**](https://stackoverflow.com/a/292502/6233477), Python uses the LEGB Rule to locate a definition. LEGB stands for:

*   _**L**, Local — Names assigned in any way within a function (_def _or_ lambda_)), and not declared global in that function._
*   _**E**, Enclosing-function locals — Name in the local scope of any and all statically enclosing functions (_def _or_ lambda_), from inner to outer._
*   _**G**, Global (module) — Names assigned at the top-level of a module file, or by executing a_ global _statement in a_ def _within the file._
*   _**B**, Built-in (Python) — Names preassigned in the built-in names module_ open,range,SyntaxError,...

In a nutshell, Python will first look at the **local** scope for a name to object mapping (e.g people = 5). If it cannot find one, it will continue going up the hierarchy until it finds one. If it doesn't find a mapping, it will raise an exception.

To shed some more light on this let's take a step back and analyse each of the points listed above. I'll do so in reverse order because that is the way we write Python code, as you'll see in a moment.

## Built-in

As the name suggests, **Built-in** refer to mappings which are built into Python. Because they're **built-in**, we don't have to do anything but call the name in order to use it.

Here's an example of using the `range()` **built-in**:

```python
# built-in
print range(5)

# Output:
[0, 1, 2, 3, 4] # built-in
```

## Global

Let's now add a **global** name called range and see what happens:

```python
# built-in
print range(5)

# global
range = 'this is a global "range"'
print range

# Output:
[0, 1, 2, 3, 4] # built-in
this is a global "range" # global
```

We can see that the **global** name overrode the **built-in** name given that the **global** string - this is a global "range" - was printed. Looking at the LEGB rule tells us that this is expected behavior given that the **global** scope has a higher precedence than the **built-in** scope.

Let's just double check that we're right though. Let's move the **built-in** below the **global** and see what happens:

```
this is a global "range"
Traceback (most recent call last):
  File line 13, in print range(5)
TypeError: 'str' object is not callable

Process finished with exit code 1
``` 

We encounter an error when the interpreter reaches the print range(5) line. This is because the `range()`  **built-in** function no longer exists because it has been replaced by our **global** range string.

_Side note: This is known as Shadowing. I'll expand on Shadowing in another post but suffice to say that you should never shadow **built-ins** otherwise you'll run into issues such as this. Also note that **built-ins** are available in **all** namespaces, not just global._

## Enclosing

The below code has been expanded to include an **enclosing** scope example:

```python
# built-in
print range(5)

# global
range = 'this is a global "range"'
print range

def main():
    # enclosing
    range = 'this is an enclosed "range"'

    def enclosing():
        print range

    enclosing()

main()
```

What I've done here is enclosed the `enclosing()` function inside of the `main()` function. This is also known as nesting.

Note that the `enclosing()` function does not have a definition for range in its scope and therefore it needs to go up the hierarchy to see if one exists there. Let's see what it finds:

```python
[0, 1, 2, 3, 4] # built-in
this is a global "range"  # global
this is an enclosed "range"  # enclosing()
```

Because `main()` is the next level above `enclosing()` in the hierarchy, `main()`'s  range mapping is used.

But what would happen if `main()` didn't have a mapping for range? As per the code below, `enclosing()` would have no choice but to look higher in the hierarchy for a mapping:

```python
# built-in
print range(5)

# global
range = 'this is a global "range"'
print range

def main():
    def enclsoing():
        print range

    enclsoing()

main()
```

This would result in `enclosing()` printing the **global** mapping instead, like so:

```python
[0, 1, 2, 3, 4] # built-in
this is a global "range" # global
this is a global "range" # enclosing()
```

## Local

Local, as the name suggests, is a name to object mapping that is **local** to the scope. In other words, Python does not need to search the hierarchy for the mapping, it is locally defined like so:

```python
# built-in
print range(5)

# global
range = 'this is a global "range"'
print range

def main():
    # enclosing
    range = 'this is an enclosed "range"'

    def enclosing():
        # local
        range = 'this is a local "range"'
        print range

    enclosing()

main()
```

This code results in the following output:

```python
[0, 1, 2, 3, 4] # built-in
this is a global "range" # global
this is a local "range" # local
```

Note that the **enclosing** definition is no longer used because, as per the order of operation listed at the top of this post, **local** scope has precedence over an **enclosing **scope.