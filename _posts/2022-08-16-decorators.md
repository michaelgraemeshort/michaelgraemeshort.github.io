---
layout: post
title: Building Decorators in Python
subtitle: Nested functions ahoy
---

Decorators. Weird, right? Why do I need nested functions? How many nested functions do I need? How do I know which parameters to assign to each?

It's easy. There are two things to understand.

**The First Thing**

A decorator is just syntactic sugar. This

```python
@double
def add(x, y):
    return x + y
```

executes this behind the scenes:

```python
add = double(add)
```

Now, `add` invokes `double(add)`.

Likewise, `add(3, 4)` invokes `double(add)(3, 4)`.

Note: that's two sets of parentheses. Which leads us to:

**The Second Thing**

Two sets of parentheses means two functions - an outer, and an inner.

Accordingly, three sets of parentheses means three functions - an outer, a middle, and an inner. For example, in:

```python
@multiply(2)        # Fancy decorator with an argument
def add(x, y):
    return x + y
``` 

`add(3, 4)` invokes `multiply(2)(add)(3, 4)`. Keep this in mind for the next section.

**Building a Decorator**

Now, we can begin to define `multiply`.

`multiply` takes one argument (the multiplier, `2` in this example) and returns a function.

```python
def multiply(multiplier):
    def middle():
        pass
    return middle
```

`middle` takes one argument (the function `add`) and returns another function.

```python
def multiply(multiplier):
    def middle(fn):
        def inner():
            pass
        return inner
    return middle
```

The inner function takes two arguments (`3` and `4`, in this example) and returns whatever you want it to return. You now have all four arguments to play with.

```python
def multiply(multiplier):
    def middle(fn):
        def inner(x, y):
            return fn(x, y) * multiplier
        return inner
    return middle
```

**To Recap:**

Just ask yourself what will actually be invoked when you call the decorated function. Each new set of parentheses means another nested function, and their contents indicate which parameters go where.

**A layer of polish**

The same decorator, polished:

```python
from functools import wraps


def multiply(multiplier):
    def middle(fn):
        @wraps(fn)
        def wrapper(*args, **kwargs):
            return fn(*args, **kwargs) * multiplier
        return wrapper
    return middle
```

Note:
- `inner` is now called `wrapper`. This is a common Python idiom. It "wraps" `fn`.
- `wrapper` can now accept any number of positional and keyword arguments. `multiply` can therefore be used to decorate other functions with other parameters.
- `wrapper` has been decorated with `functools.wraps` and the name of the wrapped function. Without this, if you call `help` or `__doc__` on the wrapped function, you will get the docstring of the wrapper instead.

Questions? Errors? Let me know.

Until next time.