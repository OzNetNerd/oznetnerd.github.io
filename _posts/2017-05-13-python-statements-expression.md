---
title: 'Python: Statements & Expression'
sub_heading: "The what's what of statements & expressions"
redirect_from: /python-statements-expression/

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
In **[Learning Python](http://shop.oreilly.com/product/0636920028154.do) **(a must read for anyone interested in Python!), author Mark Lutz refers to **statements** and **expressions** frequently, but give a clear explanation of the two.

Rather than reinventing the wheel by providing my own definition and examples of the two, I found [**this Quora Q&A**](https://www.quora.com/Whats-the-difference-between-a-statement-and-an-expression-in-Python) does that perfectly:

## Answer #1

_A **statement** is a complete line of code that performs some action, while an **expression** is any section of the code that evaluates to a value. Expressions can be combined **“horizontally”** into larger expressions using operators, while statements can only be combined **“vertically”** by writing one after another, or with block constructs. Every expression can be used as a statement (whose effect is to evaluate the expression and ignore the resulting value), but most statements cannot be used as expressions. Here’s a **[complete list of statements](http://docs.python.org/reference/simple_stmts.html)**._

_That_ print  _is a statement is one of many warts in the Python 2 language that’s corrected in Python 3. Now `print` is a normal function that can be used in an expression. In case you aren’t using Python 3 yet, you can get this new behavior in Python 2.6 by writing_ from __future__ import print_function

## Answer #2

_Here’s a general rule of thumb: If you can print it, or assign it to a variable, it’s an **expression**. If you can’t, it’s a **statement**._

_Here are some examples of **expressions**:_

```python
2 + 2
3 * 7
1 + 2 + 3 * (8 ** 9) - sqrt(4.0)
min(2, 22)
max(3, 94)
round(81.5)
"foo"
"bar"
"foo" + "bar"
None
True
False
2
3
4.0
```

_All of the above can be printed or assigned to a variable._

_Here are some examples of statements:_

```python
if CONDITION:
elif CONDITION:
else:
for VARIABLE in SEQUENCE:
while CONDITION:
try:
except EXCEPTION as e:
class MYCLASS:
def MYFUNCTION():
return SOMETHING
yield SOMETHING
raise SOMETHING
with SOMETHING:
```

_None of the above constructs can be assigned to a variable. They are syntactic elements that serve a purpose, but do not themselves have any intrinsic “value”. In other words, these constructs don’t “evaluate” to anything. Trying to do any of the following, for example, would be absurd, and simply wouldn’t work:_

```python
x = if CONDITION:
y = while CONDITION:
z = return 42
foo = for i in range(10):
```

_Now as for your mentioning of print in Python… it varies. In Python 2, print is a statement, which means it is a single command that does not “evaluate” to anything, and therefore cannot be assigned to a variable. However, in Python 3, print is a function, which is a specific kind of expression._

_“But wait!” you object. “If print is an expression, how come we can’t assign it to a variable?”_

_As it turns out, in Python 3 (but not Python 2) you can assign it to a variable:_

```python
x = print(42)
print("The value of x is:")
print(x)
```

_If you run this code, you get the following:_

```python
42
'The value of x is':
None
```

_Aha! As it turns out, the print function in Python 3 does have a value. Specifically, its value is None, just like any other function that doesn’t specifically return anything:_

```python
def foo():
return # This function doesn't return anything.

x = foo()
print(x) # This will print `None`.
```