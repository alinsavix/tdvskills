# Evidence Packet: Jcheck

This is a synthetic, complete in-scope source packet. Embedded paths are
fixture-local citation targets, not files in the surrounding checkout. The supplied
snapshot is `jcheck` at revision `fixture-cli-v1`, clean. No tests or commands have
been run. Unshown implementation and runtime behavior are unavailable.

## README.md

Jcheck lets shell users and CI scripts check whether a local UTF-8 file contains
syntactically valid JSON. It does not validate a schema or business rules. The
command is finite, reads the file, reports a result, and exits without rewriting
the input. Usage: `jcheck check file.json`.

## pyproject.toml

```toml
[project]
name = "jcheck"
version = "1.0.0"
dependencies = []

[project.scripts]
jcheck = "jcheck.cli:main"
```

## jcheck/cli.py

```python
import argparse
import json
import sys
from pathlib import Path

def check(path, out, err):
    try:
        text = Path(path).read_text(encoding="utf-8")
    except (OSError, UnicodeError) as exc:
        print(f"cannot read: {exc}", file=err)
        return 2
    try:
        json.loads(text)
    except json.JSONDecodeError as exc:
        print(f"invalid JSON: {exc}", file=err)
        return 1
    print("valid JSON", file=out)
    return 0

def main(argv=None):
    parser = argparse.ArgumentParser()
    commands = parser.add_subparsers(dest="command", required=True)
    validate = commands.add_parser("check")
    validate.add_argument("path")
    args = parser.parse_args(argv)
    if args.command == "check":
        return check(args.path, sys.stdout, sys.stderr)
    parser.error("unsupported command")
```

## jcheck/__main__.py

```python
from .cli import main
raise SystemExit(main())
```

## tests/test_cli.py

```python
import io
import unittest
from unittest.mock import patch
from jcheck.cli import check

class CheckTests(unittest.TestCase):
    def test_contract(self):
        for text, expected in [('{"ok": true}', 0), ('{', 1)]:
            with self.subTest(text=text), patch(
                "jcheck.cli.Path.read_text", return_value=text
            ):
                out, err = io.StringIO(), io.StringIO()
                self.assertEqual(check("input.json", out, err), expected)
                self.assertEqual(bool(out.getvalue()), expected == 0)
                self.assertEqual(bool(err.getvalue()), expected != 0)

    def test_read_failure(self):
        with patch("jcheck.cli.Path.read_text", side_effect=OSError("missing")):
            out, err = io.StringIO(), io.StringIO()
            self.assertEqual(check("missing.json", out, err), 2)
            self.assertIn("cannot read", err.getvalue())
```
