---
date: "2026-07-17T21:21:00.00Z"
published: true
slug: PythonBugs
tags:
  - Python Mutables
  - Closures
  - Class Arguments
  - value interning
time_to_read: 5
title: Python's Sneakiest Bugs.
description: The Mutable State Disease A Field Guide to Python's Sneakiest Bugs
type: post
---

## Why Your Code Lies to You

Every Python developer eventually hits a moment where their code does something that seems impossible. A function that should return a fresh list somehow remembers everything it's ever seen. Two "independent" class instances mysteriously share data. A loop that should produce three different values produces the same one three times.

None of this is magic, and none of it is a bug in Python itself. It's all one single root cause, wearing ten different costumes:

> **Python passes and stores objects by reference. Some objects are mutable. Multiple names can point to the same object — and mutating it through one name changes what every other name sees.**

That's it. That's the whole disease. Everything below is a symptom.

---

## Symptom 1: The Mutable Default Argument

```python
def add_log(entry, log=[]):
    log.append(entry)
    return log

print(add_log("server started"))   # ['server started']
print(add_log("user logged in"))   # ['server started', 'user logged in']
```

You'd expect a fresh list every call. Instead, the list keeps growing.

**Why:** default argument values are evaluated exactly once — at function _definition_ time, not at call time. Python builds `[]` a single time and reuses that same list object for every call that doesn't supply `log` explicitly. It's not "positional vs keyword" — it's _one-time evaluation of a mutable object_.

**The fix:**

```python
def add_log(entry, log=None):
    if log is None:
        log = []
    log.append(entry)
    return log
```

`None` is immutable and safe to reuse as a default; the real list gets built fresh, inside the function body, every single call.

---

## Symptom 2: Closures That Capture the Wrong Thing

```python
funcs = []
for i in range(3):
    funcs.append(lambda: i)

print([f() for f in funcs])   # [2, 2, 2], not [0, 1, 2]
```

**Why:** closures in Python capture variables **by reference**, not by value. All three lambdas don't store "0," "1," "2" — they all point at the _same_ variable `i`. By the time you call them, the loop has finished and `i` is permanently `2`.

**The fix — weaponize the mutable-default mechanism on purpose:**

```python
funcs = []
for i in range(3):
    funcs.append(lambda i=i: i)
```

`i=i` gets evaluated _right now_, on this exact iteration, and frozen into that lambda's own default value. Now `[f() for f in funcs]` gives `[0, 1, 2]`.

---

## Symptom 3: The Shallow Copy That Isn't

```python
original = [[1, 2, 3], [4, 5, 6]]
copy = original.copy()
copy[0][0] = 99
print(original)   # [[99, 2, 3], [4, 5, 6]]
```

**Why:** `.copy()` clones the _outer_ list only. The inner lists are not duplicated — both `original` and `copy` hold references to the _same_ inner list objects. `copy[0][0] = 99` doesn't touch the outer list at all; it reaches into a shared inner object and mutates it in place.

**The rule:** a shallow copy is safe when its elements are immutable (ints, strings, tuples — any "change" is actually a rebind, not a mutation). It's dangerous when elements are mutable (lists, dicts, custom objects), because in-place mutation is visible through every reference to that object.

**The fix for real independence:**

```python
import copy
copy_deep = copy.deepcopy(original)
```

---

## Symptom 4: Class Attributes Shared by Every Instance

```python
class ShoppingCart:
    items = []

    def add_item(self, item):
        self.items.append(item)

cart1, cart2 = ShoppingCart(), ShoppingCart()
cart1.add_item("apple")
cart2.add_item("banana")

print(cart1.items)   # ['apple', 'banana']
print(cart2.items)   # ['apple', 'banana']
```

**Why:** `items = []` at the class level is evaluated once, at class-definition time — same disease as Symptom 1, just hosted on a class instead of a function. Every instance that doesn't shadow it with its own instance attribute shares the exact same list.

**The fix:**

```python
class ShoppingCart:
    def __init__(self):
        self.items = []
```

Now `self.items` is created fresh, per instance, at `__init__` time.

---

## Symptom 5: The Compounding Case — Trees of Shared State

```python
class Node:
    def __init__(self, value, children=[]):
        self.value = value
        self.children = children

    def add_child(self, child):
        self.children.append(child)

root, branch1, branch2 = Node("root"), Node("branch1"), Node("branch2")
root.add_child(branch1)
root.add_child(branch2)

print(len(root.children))      # 2
print(len(branch1.children))   # 2 — not 0!
```

This is Symptom 1 at its nastiest. `root`, `branch1`, and `branch2` never had three separate empty lists — they all pointed at _one_ shared list from the moment `Node.__init__`'s default was created. Appending a child to `root` silently pollutes `branch1` and `branch2` too, even though neither ever called `add_child`. In a real tree structure, this manifests as objects that have nothing to do with each other mysteriously sharing data — a bug that can take hours to trace back to one `def __init__(self, x=[])`.

**Fix:** same medicine, every time — `children=None`, then `self.children = children if children is not None else []`.

---

## Symptom 6: Decorators Run at Definition Time, Not Call Time

```python
def memoize(func):
    cache = {}
    def wrapper(*args):
        if args in cache:
            return cache[args]
        result = func(*args)
        cache[args] = result
        return result
    return wrapper

@memoize
def get_user_permissions(user_id, permissions_list=[]):
    permissions_list.append("read")
    return permissions_list

print(get_user_permissions(1))   # ['read']
print(get_user_permissions(2))   # ['read', 'read']
print(get_user_permissions(1))   # ['read', 'read']  -- cache hit, no third append
```

`@memoize` is sugar for `get_user_permissions = memoize(get_user_permissions)`, which **runs immediately**, at definition time, before your first `print` ever executes. That's when `cache = {}` gets created — once, for the entire life of the program — and it's also when the mutable default `permissions_list=[]` gets locked in.

The nightmare: this cache doesn't just fail to speed things up — it can **corrupt results across unrelated keys**, because every "different" call was secretly mutating the same shared list the whole time. In a permissions system, that's not a performance bug, that's a security incident.

---

## Symptom 7: Identity Caching (`is` vs `==`)

```python
a, b = 256, 256
print(a is b)     # True

x, y = 257, 257
print(x is y)     # False (usually)
```

**Why:** CPython pre-caches small integers (-5 to 256) at startup — a pure implementation detail called small integer interning. `256` always points at the same cached object; `257` falls outside the cache and typically gets a fresh object each time it's created. Same idea applies to short, identifier-like strings (`"hello"` is interned; `"hello world"` usually isn't, because it contains a space).

**The rule that matters:** none of this is a language guarantee — it's a CPython optimization detail that can change between versions or implementations. Never use `is` to compare values. Reserve it exclusively for `None`, `True`, `False`, and singleton sentinel checks. Use `==` for everything else.

---

## Symptom 8: Generators Are Single-Use

```python
def get_even_numbers(numbers):
    for n in numbers:
        if n % 2 == 0:
            yield n

evens = get_even_numbers([1, 2, 3, 4, 5, 6])

print(sum(evens))    # 12
print(list(evens))   # []
print(sum(evens))    # 0
```

**Why:** a list is a materialized collection sitting in memory — you can iterate it endlessly, from the start, every time. A generator is not a container; it's a **paused function**, holding a single instruction pointer and its local state. It can only move forward. Once it reaches the end, there's no rewind — it just yields nothing, silently, forever. No error, no crash — just quietly wrong results downstream.

**The fix:** materialize once with `list(...)` if you need to iterate the same data more than once, or call the generator _function_ again to get a brand-new generator object.

---

## Symptom 9: `**kwargs` Collisions

```python
def process_order(item, quantity=1, **options):
    print(f"{quantity}x {item}, options: {options}")

order = {"item": "widget", "quantity": 5, "priority": "high"}

process_order(**order)                 # fine: 5x widget, options: {'priority': 'high'}
process_order("gadget", **order)       # TypeError: multiple values for 'item'
```

**Why:** `"gadget"` binds to `item` positionally; `**order` then tries to bind its own `"item"` key to the same parameter by keyword. Python won't silently pick a winner when a parameter gets two conflicting values in one call — it raises `TypeError` immediately.

This shows up constantly when a config or payload dict gets unpacked alongside explicit positional arguments — harmless until one dict key happens to collide with a parameter name, at which point production breaks with a message that doesn't obviously point at the actual cause.

---

## Symptom 10: The Final Boss — Compounding Mutable State

A function can carry _multiple_ independent mutable defaults at once:

```python
def build_pipeline(steps=[], cache={}):
    def run(data):
        key = str(data)
        if key in cache:
            return cache[key]
        result = data
        for step in steps:
            result = step(result)
        cache[key] = result
        return result
    steps.append(lambda x: x * 2)
    return run
```

Fixing `steps` by passing your own list explicitly _looks_ like a complete fix — but `cache` is still on its shared default, silently poisoning results across every "independent" pipeline built this way. A partially-fixed function is often more dangerous than a fully broken one, because it looks correct while still hiding shared state.

**The lesson:** check _every_ default argument in a function signature, not just the first one you notice.

---

## The One Rule Underneath All Ten

If you take away nothing else, take this:

> Don't ask _"what value does this variable hold?"_ Ask _"what object does this name point to, and who else points to the same one?"_

Every symptom above resolves the moment you can answer that question. Mutable defaults, shared class attributes, decorator closures, generator exhaustion, `is` comparisons — they're all the same question in different clothes: **is this object shared, and is it mutable?**

Trace object identity and lifetime through a piece of code, and every one of these bugs becomes obvious before you even run it.
