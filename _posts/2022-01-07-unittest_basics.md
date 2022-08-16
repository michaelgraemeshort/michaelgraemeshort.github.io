---
layout: post
title: unittest Basics
---

**Very Quick Reference**

1. Name file `test_<whatever>.py` so it can be detected by `unittest`. In the file:
2. `import unittest`
3. `import` whatever you're testing 
4. Define a class, perhaps called `<Whatever>Tests`, that inherits from `unittest.TestCase`
5. Make assertions. Note weird(er) syntax for testing error handling
6. Finish with the command line entry point for the test runner `unittest.main`, so you can
7. Run file from command line using `python3 -m unittest <path-to-file>`

**Example**

```python
import unittest

from cards_exercise import Card


class CardTests(unittest.TestCase):
    def setUp(self):
        self.card = Card("Hearts", "A")

    def test_init(self):
        self.assertEqual(self.card.suit, "Hearts")
        self.assertEqual(self.card.value, "A")
        with self.assertRaises(ValueError):
            self.card = Card("Spam", "42")


if __name__ == "__main__":
    unittest.main()
```

**Notes**

- Just use `pytest`
- `setUp` and `tearDown` methods can be used to define instructions to be executed before and after each test
- The `-m` flag in `python3 -m <etc>` runs the module specified thereafter (`unittest`, in this case) as a script

**Resources**
- [Real Python: Getting Started with Testing in Python](https://realpython.com/python-testing/)
- [Official Unittest Documentation](https://docs.python.org/3/library/unittest.html)