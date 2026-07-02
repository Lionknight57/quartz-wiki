---
title: Python
tags: [language, python, scripting, ai-ml]
related: [AI-ML, Data-Structures, TypeScript-JavaScript]
sources: [Python/Core Python Getting Started.md]
updated: 2026-04-19
---

# Python

Dynamically typed, interpreted language. Widely used in AI/ML, scripting, data science, and backend services.

---

## Setup and REPL

```bash
# Install from https://www.python.org
python          # start REPL (Windows: from PowerShell)
python script.py  # run a file
```

**REPL tips:**
- `_` — last evaluated value in the REPL
- `help(math)` — module help
- `help(math.factorial)` — function help
- `Ctrl+Z` + Enter — exit (Windows)

---

## Syntax fundamentals

**Whitespace is significant** — indentation replaces `{}`:

```python
if x > 0:
    print("positive")   # 4 spaces (PEP 8 standard)
    return x
```

**Comments:** `# hash`

**Docstrings:** triple-quoted string immediately after `def` or `class`:

```python
def factorial(n):
    """Return n! — the product of all integers 1..n."""
    ...
```

**Integer division:** `//` (use instead of `/` when you want whole number result)

```python
10 / 3    # 3.333...  (float)
10 // 3   # 3         (integer)
```

---

## Imports and modules

```python
import math
math.factorial(5)              # fully qualified

from math import factorial     # import specific name
factorial(5)

from math import factorial as fac   # alias
fac(5)

import sys
sys.argv[1]   # first command-line argument
```

**`__name__` guard** — run code only when executed directly (not imported):

```python
def main():
    ...

if __name__ == '__main__':
    main()
```

**Shebang** (Unix) — specify interpreter on first line:

```python
#!/usr/bin/env python3
```

---

## Types and objects

**Everything is an object** — every value has an id, type, and value:

```python
type(42)         # <class 'int'>
type("hello")    # <class 'str'>
dir(42)          # list all attributes/methods on an object
id(x)            # memory address (unique identity)

x is y           # identity equality (same object in memory)
x == y           # value equality
```

Python has **no value types** — all variables are references. Arguments are **passed by object reference** (not by value or pointer).

**LEGB scope rule** — name lookup order:
1. **L**ocal — inside the current function
2. **E**nclosing — enclosing function scopes (closures)
3. **G**lobal — module-level
4. **B**uilt-in — `len`, `range`, etc.

```python
x = 10          # global

def outer():
    x = 20      # enclosing
    def inner():
        global x   # rebind the global (use sparingly)
        x = 30
```

---

## Built-in collections

### Tuples — immutable sequences

```python
t = (1, 2, 3)
t = 1, 2, 3          # parentheses optional
single = (42,)       # comma required for single-element tuple

# Unpacking (destructuring)
a, b, c = t
min_val, max_val = min(t), max(t)

# Swap without temp variable
a, b = b, a

len(t)               # length
3 in t               # membership test
t1 + t2              # concatenation
```

### Strings — immutable sequences of characters

```python
s = "hello"
len(s)               # 5
s + " world"         # concatenation
s.upper()
s.split(",")         # list of substrings
",".join(["a","b","c"])   # "a,b,c" — join is faster than + loop

# Partition — splits into 3-tuple (before, sep, after)
"user@example.com".partition("@")  # ('user', '@', 'example.com')
_, _, domain = "user@example.com".partition("@")  # _ for unused parts

# Formatting
"{0} is {1}".format("Python", "great")
"{name} is {adj}".format(name="Python", adj="great")
f"{'Python'} is {'great'}"    # f-string (Python 3.6+)
f"{2 + 2}"                    # expressions in f-strings
```

### Range — arithmetic progression of integers

```python
range(5)          # 0, 1, 2, 3, 4
range(2, 10)      # 2..9
range(0, 10, 2)   # 0, 2, 4, 6, 8  (step)

for i in range(5):
    print(i)

# enumerate — (index, value) pairs
for i, val in enumerate(["a", "b", "c"]):
    print(i, val)   # 0 a, 1 b, 2 c
```

### Lists — mutable sequences

```python
lst = [1, 2, 3]
lst.append(4)          # add to end
lst.insert(1, 99)      # insert at index
lst.extend([5, 6])     # append multiple
del lst[0]             # remove by index
lst.remove(99)         # remove by value (first match)

lst[0]                 # index (0-based)
lst[-1]                # last element
lst[1:3]               # slice [start:stop] — stop excluded
lst[:]                 # shallow copy

2 in lst               # membership
lst.index(2)           # find position
lst.count(2)           # count occurrences

lst.sort()             # in-place sort
lst.sort(key=len)      # sort by key function
lst.reverse()          # in-place reverse

sorted(lst)            # new sorted list (non-mutating)
reversed(lst)          # returns iterator (non-mutating)
```

### Dictionaries — key-value mapping

Keys must be **immutable** (strings, numbers, tuples). Insertion order preserved (Python 3.7+).

```python
d = {"name": "Ken", "age": 42}
d = dict(name="Ken", age=42)   # alternative

d["name"]              # access (KeyError if missing)
d.get("city", "N/A")  # safe access with default

d["city"] = "Auckland" # add/update
del d["age"]           # remove key

d.update({"age": 43, "city": "Sydney"})   # merge/update

# Iteration
for key in d:           # keys
for val in d.values():  # values
for k, v in d.items():  # key-value pairs

"name" in d            # membership test on keys
d.copy()               # shallow copy
```

### Sets — unordered unique elements

```python
s = {1, 2, 3}
s = set()              # empty set (not {} — that's a dict)
s = set([1, 2, 2, 3])  # deduplicate list → {1, 2, 3}

s.add(4)
s.update({5, 6})       # add multiple
s.remove(4)            # KeyError if missing
s.discard(99)          # safe remove — no error

# Set algebra
a | b    # union
a & b    # intersection
a - b    # difference (in a but not b)
a ^ b    # symmetric difference
a <= b   # subset
a >= b   # superset
a.isdisjoint(b)   # no common elements
```

---

## Control flow

```python
# if / elif / else
if x > 0:
    ...
elif x == 0:
    ...
else:
    ...

# while
while condition:
    ...
    break     # exit loop
    continue  # skip to next iteration

# for — iterates any iterable
for item in collection:
    ...

# Conditional expression (ternary)
result = "yes" if condition else "no"
```

---

## Functions

```python
def greet(name, greeting="Hello"):   # default argument at end
    """Greet the named person."""
    return f"{greeting}, {name}!"

# Call with positional or keyword args
greet("Ken")
greet("Ken", greeting="Hi")
greet(name="Ken", greeting="Hi")

# *args — variable positional arguments
def total(*numbers):
    return sum(numbers)

# **kwargs — variable keyword arguments
def show(**attrs):
    for k, v in attrs.items():
        print(f"{k}: {v}")

# Functions are objects — assign, pass, return
def apply(func, value):
    return func(value)

apply(str.upper, "hello")   # "HELLO"
```

**Lambda** — anonymous single-expression function:

```python
square = lambda x: x ** 2
sorted(items, key=lambda x: x.name)
```

---

## Exception handling

```python
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Can't divide by zero")
except (TypeError, ValueError) as e:   # multiple types
    print(f"Type/value error: {e}")
except Exception as e:                  # catch-all
    print(f"Unexpected: {e}")
else:
    print("No exception occurred")      # runs if no exception
finally:
    print("Always runs")               # cleanup

# Raise
raise ValueError("Invalid input")

# pass — empty exception handler (suppress silently)
try:
    ...
except SomeError:
    pass
```

---

## Comprehensions

```python
# List comprehension
squares = [x**2 for x in range(10)]
evens   = [x for x in range(20) if x % 2 == 0]

# Dict comprehension
word_lengths = {word: len(word) for word in words}

# Set comprehension
unique_lengths = {len(word) for word in words}

# Generator expression (lazy — no list created)
total = sum(x**2 for x in range(1000000))
```

---

## Classes

```python
class Animal:
    """Base class for animals."""

    species_count = 0    # class variable (shared)

    def __init__(self, name, sound):
        self.name = name     # instance variable
        self.sound = sound
        Animal.species_count += 1

    def speak(self):
        return f"{self.name} says {self.sound}"

    def __str__(self):    # string representation
        return f"Animal({self.name})"

    def __repr__(self):   # developer representation
        return f"Animal(name={self.name!r}, sound={self.sound!r})"

class Dog(Animal):
    def __init__(self, name):
        super().__init__(name, "Woof")

    def fetch(self):
        return f"{self.name} fetches the ball"

dog = Dog("Rex")
isinstance(dog, Animal)  # True
```

---

## Standard library highlights

```python
import os
os.path.join("dir", "file.txt")
os.listdir(".")

import sys
sys.argv          # command-line arguments
sys.exit(0)       # exit with code

import json
json.dumps({"key": "value"})   # dict → JSON string
json.loads('{"key": "value"}') # JSON string → dict

from datetime import datetime
datetime.now()
datetime.strptime("2026-04-19", "%Y-%m-%d")

import re
re.findall(r'\d+', "abc 123 def 456")  # ['123', '456']

from pathlib import Path
p = Path("C:/Users/ken/file.txt")
p.read_text()
p.exists()
```

---

## See also
- [[AI-ML]] — Python is the primary language for ML/AI
- [[Data-Structures]] — algorithms relevant to Python collections
- [[TypeScript-JavaScript]] — comparison: static vs dynamic typing
