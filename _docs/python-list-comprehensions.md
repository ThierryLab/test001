---
layout: guide
title: "Python List Comprehensions"
description: "Learn to build, filter and transform lists in one readable line with Python list comprehensions, plus when to use alternatives."
category: "Python"
difficulty: "Beginner"
read_time: "7 min"
last_updated: "October 2026"
tags: ["Python", "Lists", "Comprehensions", "Fundamentals"]
---

# Python List Comprehensions

A list comprehension builds a new list from an iterable in a single expression. This guide is for Python beginners who already know `for` loops and lists. By the end you will be able to write, filter, nest and read comprehensions, and know when a plain loop or a generator is the better choice.

## Prerequisites

- Python 3.8 or later (only the [walrus operator](#assignment-expressions-python-38) section needs 3.8; everything else works on any Python 3).
- Basic knowledge of `for` loops, `if` statements and lists.

## Basic syntax

A comprehension has three parts: an **expression**, a **loop** and an optional **condition**.

```python
[expression for item in iterable if condition]
```

Here is the same result written as a loop and as a comprehension:

```python
# Loop version
squares = []
for n in range(1, 6):
    squares.append(n * n)

# Comprehension version
squares = [n * n for n in range(1, 6)]
print(squares)  # [1, 4, 9, 16, 25]
```

Read it left to right as: "*n × n*, for each *n* in `range(1, 6)`".

## Transforming items

The expression can be any valid Python expression: a method call, a function call, arithmetic or an f-string.

```python
names = ["  alice ", "BOB", " Carol"]
clean = [name.strip().title() for name in names]
print(clean)  # ['Alice', 'Bob', 'Carol']

prices = [9.5, 12, 3.25]
labels = [f"${p:.2f}" for p in prices]
print(labels)  # ['$9.50', '$12.00', '$3.25']
```

## Filtering with if

Add `if` at the **end** to keep only the items that match.

```python
evens = [n for n in range(10) if n % 2 == 0]
print(evens)  # [0, 2, 4, 6, 8]
```

You can chain several `if` clauses. Each one must be true for the item to be kept, so they act like `and`:

```python
multiples_of_6 = [n for n in range(30) if n % 2 == 0 if n % 3 == 0]
print(multiples_of_6)  # [0, 6, 12, 18, 24]
```

> **Note:** `if n % 2 == 0 and n % 3 == 0` gives the same result and is usually easier to read.

## Choosing values with if/else

To **choose between two values** instead of dropping items, put a conditional expression (`a if condition else b`) at the **start**:

```python
numbers = [1, 2, 3, 4]
parity = ["even" if n % 2 == 0 else "odd" for n in numbers]
print(parity)  # ['odd', 'even', 'odd', 'even']
```

| Goal | Position | Example |
|---|---|---|
| Drop items | `if` after the loop | `[n for n in nums if n > 0]` |
| Replace items | `if … else …` before the loop | `[n if n > 0 else 0 for n in nums]` |

> **Warning:** `[n for n in nums if n > 0 else 0]` is a `SyntaxError`. An `else` is only allowed in the expression part.

## Multiple loops

Several `for` clauses run like nested loops, **outer loop first**:

```python
pairs = [(x, y) for x in range(3) for y in "ab"]
print(pairs)
# [(0, 'a'), (0, 'b'), (1, 'a'), (1, 'b'), (2, 'a'), (2, 'b')]
```

This is equivalent to:

```python
pairs = []
for x in range(3):
    for y in "ab":
        pairs.append((x, y))
```

A common use is flattening a list of lists:

```python
matrix = [[1, 2, 3], [4, 5, 6]]
flat = [n for row in matrix for n in row]
print(flat)  # [1, 2, 3, 4, 5, 6]
```

## Nested comprehensions

A comprehension can be the expression of another comprehension. This builds a list of lists. Here it transposes a matrix:

```python
matrix = [[1, 2, 3], [4, 5, 6]]
transposed = [[row[i] for row in matrix] for i in range(len(matrix[0]))]
print(transposed)  # [[1, 4], [2, 5], [3, 6]]
```

> **Note:** For this specific task, `[list(col) for col in zip(*matrix)]` is shorter. If a nested comprehension needs more than a moment to read, use a regular loop instead.

## Assignment expressions (Python 3.8+)

The walrus operator `:=` lets you compute a value once and reuse it in both the condition and the result:

```python
def parse(text):
    try:
        return int(text)
    except ValueError:
        return None

raw = ["10", "x", "7", ""]
numbers = [n for s in raw if (n := parse(s)) is not None]
print(numbers)  # [10, 7]
```

Without `:=` you would need to call `parse(s)` twice or write a loop.

## Other comprehensions

The same syntax works for sets, dictionaries and generators. Only the brackets change.

| Type | Syntax | Result |
|---|---|---|
| List | `[x * 2 for x in data]` | `list` (ordered, allows duplicates) |
| Set | `{len(w) for w in words}` | `set` (unique values) |
| Dictionary | `{k: v for k, v in pairs}` | `dict` |
| Generator expression | `(x * 2 for x in data)` | lazy iterator, values produced on demand |

```python
words = ["hi", "hey", "yo"]

lengths = {len(w) for w in words}
print(lengths)  # {2, 3}

word_lengths = {w: len(w) for w in words}
print(word_lengths)  # {'hi': 2, 'hey': 3, 'yo': 2}

prices = {"apple": 1.2, "pear": 0.8}
by_price = {price: name for name, price in prices.items()}
print(by_price)  # {1.2: 'apple', 0.8: 'pear'}
```

### When to use a generator expression

If you only iterate over the result once, for example to pass it to `sum()`, `max()`, `any()` or `"".join()`, use a generator expression. It doesn't build the whole list in memory:

```python
total = sum(n * n for n in range(1_000_000))
has_negative = any(x < 0 for x in [3, -1, 5])
```

When a generator expression is the only argument, you can drop its own parentheses, as shown above.

## Scope of the loop variable

In Python 3 the loop variable of a comprehension does **not** leak into the surrounding scope:

```python
x = "outer"
values = [x for x in range(3)]
print(x)  # outer
```

Variables assigned with `:=` are the exception: they **are** bound in the surrounding scope.

## Common pitfalls

### Using comprehensions only for side effects

```python
# Avoid: builds a throwaway list of None values
[print(item) for item in items]

# Prefer
for item in items:
    print(item)
```

Use a comprehension when you want the resulting list. Use a loop when you want the action.

### Late binding in lambdas

Functions created inside a comprehension look up the loop variable when they are **called**, not when they are created:

```python
funcs = [lambda: i for i in range(3)]
print([f() for f in funcs])  # [2, 2, 2]

# Fix: bind the current value as a default argument
funcs = [lambda i=i: i for i in range(3)]
print([f() for f in funcs])  # [0, 1, 2]
```

### Packing too much into one line

If a comprehension has more than two `for` clauses, complex conditions, or needs a comment to explain it, a regular loop is clearer. Readability matters more than line count.

## Comprehensions vs map and filter

| Task | Comprehension | `map` / `filter` |
|---|---|---|
| Transform | `[s.upper() for s in words]` | `list(map(str.upper, words))` |
| Filter | `[n for n in nums if n > 0]` | `list(filter(lambda n: n > 0, nums))` |
| Both | `[n * 2 for n in nums if n > 0]` | `list(map(lambda n: n * 2, filter(lambda n: n > 0, nums)))` |

Comprehensions are usually more readable, especially when you need both a filter and a transformation. `map` can be concise when you already have a named function, such as `map(str.upper, words)`.

## Measuring performance

Comprehensions are generally faster than an equivalent `for` loop that calls `.append()`, because they avoid looking up and calling the method on every iteration. Measure on your own data with `timeit`:

```bash
python -m timeit "result = [n * n for n in range(1000)]"
python -m timeit "result = []" "for n in range(1000): result.append(n * n)"
```

## Quick reference

```python
[expr for x in it]                       # transform
[expr for x in it if cond]               # filter
[a if cond else b for x in it]           # choose a value
[expr for x in it1 for y in it2]         # nested loops (x outer)
[[expr for y in it2] for x in it1]       # list of lists
[y for x in it if (y := f(x))]           # compute once (3.8+)
{expr for x in it}                       # set
{k: v for x in it}                       # dict
(expr for x in it)                       # generator (lazy)
```

## Next steps

- Practice by rewriting a few `for … append` loops in your own code as comprehensions, then decide which version reads better.
- Read the official tutorial section on [list comprehensions](https://docs.python.org/3/tutorial/datastructures.html#list-comprehensions).
- Explore generator expressions and the `itertools` module for working with large or infinite data streams.
