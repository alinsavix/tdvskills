# Evidence Packet: Spanpack

This is a synthetic, complete in-scope source packet. Embedded paths are
fixture-local citation targets, not files in the surrounding checkout. The supplied
snapshot is `spanpack` at revision `fixture-library-v1`, clean. No tests or commands
have been run. Unshown implementation and runtime behavior are unavailable.

## README.md

Spanpack is a Python library for scheduling and reporting tools that need a
normalized union of integer ranges. Callers supply closed intervals: both
endpoints are included. Overlapping and touching ranges merge. The result is a
sorted tuple of non-overlapping interval tuples. Inputs must not be mutated.
The library does not store bookings or decide scheduling policy.

```python
from spanpack import merge
assert merge([(4, 7), (1, 4)]) == ((1, 7),)
```

## pyproject.toml

```toml
[project]
name = "spanpack"
version = "1.0.0"
requires-python = ">=3.11"
dependencies = []
```

## src/spanpack/__init__.py

```python
from .intervals import merge
__all__ = ["merge"]
```

## src/spanpack/intervals.py

```python
def merge(ranges):
    ordered = sorted(ranges)
    merged = []
    for start, end in ordered:
        if start > end:
            raise ValueError("reversed interval")
        if merged and start <= merged[-1][1]:
            previous_start, previous_end = merged[-1]
            merged[-1] = (previous_start, max(previous_end, end))
        else:
            merged.append((start, end))
    return tuple(merged)
```

## tests/test_intervals.py

```python
import unittest
from spanpack import merge

class MergeTests(unittest.TestCase):
    def test_touching_and_input_ownership(self):
        ranges = [(4, 7), (1, 4)]
        self.assertEqual(merge(ranges), ((1, 7),))
        self.assertEqual(ranges, [(4, 7), (1, 4)])

    def test_empty_and_disjoint(self):
        self.assertEqual(merge([]), ())
        self.assertEqual(merge([(5, 6), (1, 2)]), ((1, 2), (5, 6)))

    def test_reversed(self):
        with self.assertRaises(ValueError):
            merge([(7, 4)])
```
