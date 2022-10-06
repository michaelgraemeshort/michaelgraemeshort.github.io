---
layout: post
title: Building Decorators in Python
subtitle: Nested functions ahoy
---

Decorators. Confusing, right? Why do I need nested functions? How many nested functions do I need? How do I know which parameters go where?

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

Two sets of parentheses means two functions - an outer function, and an inner (or nested) function. This point is crucial: `double(add)` must return a object that can be called with `(3, 4)`. 

Similarly, three sets of parentheses means three functions - an outer function, a nested function, and a nested function in the nested function. For example, in:

```python
@multiply(2)        # Fancy decorator with an argument 
def add(x, y):
    return x + y
``` 

`add(3, 4)` invokes `multiply(2)(add)(3, 4)`. Keep this in mind for the next section.

**Building a Decorator**

Now, we are going to define `multiply`.

`multiply` takes one argument (the multiplier, `2` in this example) and returns a function, which I'll call `middle`.

```python
def multiply(multiplier):
    def middle():
        pass
    return middle
```

`middle` takes one argument (the function `add`) and returns another function, which I'll call `wrapper` (just go with it).

```python
def multiply(multiplier):
    def middle(fn):
        def wrapper():
            pass
        return inner
    return middle
```

`wrapper` takes two arguments (`3` and `4`, in this example) and returns whatever you make it return. Now, you have all four arguments to play with.

```python
def multiply(multiplier):
    def middle(fn):
        def wrapper(x, y):
            return fn(x, y) * multiplier
        return inner
    return middle
```

**To Recap:**

Just ask yourself what will actually be invoked when you call the decorated function. Each new set of parentheses means another nested function, and their contents indicate which parameters go where.

**A Layer of Pythonic Polish**

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
- `wrapper` can now accept any number of positional and keyword arguments, by using the `*` and `**` operators. `multiply` can therefore be used to decorate other functions with other parameters.
- `wrapper` has been decorated with `functools.wraps` and the name of the wrapped function. Without this, if you query the metadata of the wrapped function (for example, with `help` or `__name__`), you will get the metadata of the wrapper instead.

**Addendum: Stacking Decorators**

**Q.** What does `add` invoke this time?

```python
@multiply(2)
@multiply(3)
def add(x, y):
    return x + y
```

**A.** `add = multiply(2)(multiply(3)(add))`

So `add(3, 4)` invokes `multiply(2)(multiply(3)(add))(3, 4)`.

Ponder this if you enjoy pain and misery. Otherwise, don't worry about it. It works as it should.

---

That's all for this post. Questions? Errors? Got a better name for `middle`? Let me know.