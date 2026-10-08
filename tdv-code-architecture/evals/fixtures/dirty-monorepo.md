# Evidence Packet: Counterdesk

This is a synthetic source packet, not a real local Git repository. Embedded paths
are fixture-local citation targets. Snapshot metadata and the diff below are
supplied evidence. Source blocks show working-tree contents. No tests or commands
have been run. Unshown and excluded implementations are unavailable.

## snapshot.txt

```text
repository: counterdesk
HEAD: 1111111111111111111111111111111111111111
status:
 M packages/orders/submit.py
```

## workspace.json

```json
{
  "packages": ["packages/orders", "packages/pricing", "packages/storage", "apps/website"],
  "dependencies": {
    "packages/orders": ["packages/pricing", "packages/storage"],
    "apps/website": ["packages/orders"]
  }
}
```

## packages/orders/README.md

Counterdesk accepts an operator's order through an in-process `submit` API. An
order contains line items with a unit price in integer cents and a positive integer
quantity. Pricing calculates the discounted total. Storage creates a submitted
record and returns its ID. The supplied adapter is an in-memory store; this packet
establishes no production persistence. Tax, payment and fulfillment are outside
this API. The website is outside this investigation.

## packages/orders/submit.py

```python
from pricing.api import total

def submit(items, store):
    if not items or any(
        type(item["qty"]) is not int or item["qty"] <= 0 for item in items
    ):
        raise ValueError("positive quantities required")
    cents = total(items, discount_percent=10)
    return store.create({"status": "submitted", "total_cents": cents})
```

## working-tree.diff

```diff
--- a/packages/orders/submit.py
+++ b/packages/orders/submit.py
@@ -8,2 +8,2 @@
-    cents = total(items, discount_percent=0)
+    cents = total(items, discount_percent=10)
     return store.create({"status": "submitted", "total_cents": cents})
```

## packages/pricing/api.py

```python
def total(items, discount_percent):
    if not 0 <= discount_percent <= 100:
        raise ValueError("invalid discount")
    subtotal = sum(item["price_cents"] * item["qty"] for item in items)
    return subtotal * (100 - discount_percent) // 100
```

## packages/storage/api.py

```python
class MemoryStore:
    def __init__(self):
        self.orders = {}

    def create(self, record):
        order_id = len(self.orders) + 1
        self.orders[order_id] = dict(record)
        return order_id
```

## packages/orders/tests/test_submit.py

```python
import unittest
from orders.submit import submit
from storage.api import MemoryStore

class SubmitTests(unittest.TestCase):
    def test_total(self):
        store = MemoryStore()
        order_id = submit([{"price_cents": 100, "qty": 2}], store)
        self.assertEqual(store.orders[order_id]["total_cents"], 200)
```
