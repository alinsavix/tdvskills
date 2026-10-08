# Evidence Packet: Credit Worker

This is a synthetic, complete in-scope source packet. Embedded paths are
fixture-local citation targets, not files in the surrounding checkout. The supplied
snapshot is `credit-worker` at revision `fixture-consumer-v1`, clean. No tests or
commands have been run. The broker and receipt provider implementations and actual
deployment configuration are unavailable.

## README.md

An upstream accounting service publishes credit events for existing accounts.
Each event has a stable event ID, account ID and positive amount in integer cents.
This worker records the credit, requests an email receipt, then acknowledges the
broker delivery. Repeated deliveries of an event retain the same event ID.
Different deliveries have their own delivery tag. The event producer's validation
implementation is outside this packet.

## store.py

```python
import sqlite3

def open_store(path):
    conn = sqlite3.connect(path, isolation_level="DEFERRED")
    conn.executescript("""
        CREATE TABLE IF NOT EXISTS processed (event_id TEXT PRIMARY KEY);
        CREATE TABLE IF NOT EXISTS balances (
            account_id TEXT PRIMARY KEY,
            cents INTEGER NOT NULL
        );
    """)
    return conn
```

## consumer.py

```python
def handle(delivery, conn, broker, mailer):
    event = delivery.event
    with conn:
        marker = conn.execute(
            "INSERT OR IGNORE INTO processed(event_id) VALUES (?)",
            (event["id"],),
        )
        if marker.rowcount:
            changed = conn.execute(
                "UPDATE balances SET cents = cents + ? WHERE account_id = ?",
                (event["cents"], event["account_id"]),
            )
            if changed.rowcount != 1:
                raise ValueError("unknown account")
    mailer.send_receipt(event["account_id"], event["id"])
    broker.ack(delivery.tag)
```

## mailer.py

```python
class Mailer:
    def __init__(self, http):
        self.http = http

    def send_receipt(self, account_id, event_id):
        response = self.http.post(
            "/receipts",
            json={"account_id": account_id, "event_id": event_id},
        )
        response.raise_for_status()
```

## broker.example.yaml

```yaml
# Example only; active deployment configuration is not supplied.
delivery: at-least-once
acknowledgment: manual
unacknowledged: redeliver
```

## tests/test_consumer.py

```python
import unittest
from types import SimpleNamespace
from unittest.mock import Mock
from consumer import handle
from store import open_store

class ConsumerTests(unittest.TestCase):
    def test_redelivery(self):
        conn = open_store(":memory:")
        with conn:
            conn.execute("INSERT INTO balances VALUES ('a', 0)")
        delivery = SimpleNamespace(
            event={"id": "e1", "account_id": "a", "cents": 50}, tag="first"
        )
        broker, mailer = Mock(), Mock()
        handle(delivery, conn, broker, mailer)
        delivery.tag = "second"
        handle(delivery, conn, broker, mailer)
        self.assertEqual(
            conn.execute("SELECT cents FROM balances").fetchone()[0], 50
        )
        self.assertEqual(mailer.send_receipt.call_count, 2)
        self.assertEqual(broker.ack.call_count, 2)
        conn.close()
```
