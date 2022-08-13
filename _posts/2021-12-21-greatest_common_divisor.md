---
layout: post
title: Greatest Common Divisor
---

*Return the largest positive integer that divides non-zero integers x and y.*

**The algorithm:**

Euclid's algorithm.

Mod the larger number by the smaller one. For example:

`48 % 18 = 12`

Mod the smaller number by the result of the previous step.

`18 % 12 = 6`

Repeat the previous step until there is no remainder.

`12 % 6 = 0`

The smaller number - in this case, 6 - is the greatest common divisor.

**But Why?**

The key realisation here is that the GCD must divide the difference between the two numbers given.

And the difference between that difference and the smaller of the two original numbers, and so on.

Use of the modulo operator rather than subtraction simply makes the process more efficient.

**Simple Python Example:**

```python
def greatest_common_divisor(x, y):
    if y > x:
        x, y = y, x 
    if x % y == 0:
        return y 
    return greatest_common_divisor(y, x % y)
```
