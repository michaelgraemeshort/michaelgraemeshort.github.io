---
layout: post
title: Decorators in Python
subtitle: A different perspective
---

A decorator looks like this:

```python
@double
def add(x, y):
    return x + y
```

`@double` is the decorator. It modifies the output of the function it decorates. It does this by executing

```python
add = double(add)
```
behind the scenes.

**Building a decorator**

Let's create `double` so that it doubles the output of `add`.

`add` invokes `double(add)`, so `add(2, 3)` invokes `double(add)(2, 3)`. Note the two sets of parentheses. For this to work, `double(add)` must return a function (or other callable) that can receive the arguments in the second set of parentheses.

If that made sense, proceed to "That Made Sense" below.

Otherwise, consider:

```python
def add(x):
    def inner(y):
        return x + y
    return inner
```

This new `add` is a function that returns a function, rather than a number or whatever else. It is the nested function `inner` that returns the sum of x and y.

You could pass an argument to `inner` like this:

```python
>>> add(2)(3)
5
```

`add(2)` returns `inner`, and `inner(3)` returns 5. Or you could do this:

```python
>>> why = add("why ")
>>> why("is this my life")
'why is this my life'
```

**That Made Sense**

Returning to `double(add)(2, 3)` (and our original definition of `add`), you can see that `double` must take one argument (the function `add`, in this case) and return another function.

```python
def double(fn):
    def inner():
        pass
    return inner
```

What about `inner`? It must receive the arguments ostensibly passed to `add` (2 and 3, in this case) and return double their sum.

```python
def double(fn):
    def inner(x, y):
        return (x + y) * 2
    return inner
```

Now `double(add)` returns `inner`, and `inner(2, 3)` returns `(2 + 3) * 2`.

Decorators can also take arguments. You could make a more flexible function called `multiply`, to be used like this:

```python
@multiply(2)
def add(x, y):
    return x + y
``` 

Now, `add(2, 3)` invokes `multiply(2)(add)(2, 3)`. That's three sets of parentheses, so you're going to need three functions nested within each other.

- `multiply` must take one argument (the multiplier) and return a function.
- That function must take one argument (the function `add` in this case) and return yet another function.
- That function must take the arguments (2 and 3) passed to `add` and return their sum, multiplied (in this case, by 2).

We can therefore build `multiply` as follows:

```python
def multiply(multiplier):
    def middle(fn):
        def inner(x, y):
            return (x + y) * multiplier
        return inner
    return middle
```

**More fun stuff**

- The innermost function is commonly called `wrapper`, as it "wraps" or extends the decorated function.
- `wrapper` can be made to accept any number of positional and keyword arguments by the use of the `*` and `**` operators. Then one decorator can be used to wrap different functions.
- You can decorate your wrapper with `@functools.wraps` to make it return the docstring of the wrapped function, rather than its own, when `add.__doc__` or `help(add)` is called.

Applying the above:

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