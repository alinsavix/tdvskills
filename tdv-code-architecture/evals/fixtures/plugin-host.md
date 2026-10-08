# Evidence Packet: Measuredesk

This is a synthetic source packet. Embedded paths are fixture-local citation
targets, not files in the surrounding checkout. The supplied snapshot is
`measuredesk` at revision `fixture-plugins-v1`, clean. No tests or commands have
been run. The camera SDK, example implementation and external plugin source are
unavailable; their declarations remain visible below.

## README.md

Measuredesk lets an operator collect measurement readings. A reading is a numeric
value delivered to a host-owned collection. The host receives an already selected
mapping of imported plugin modules. A plugin instance supplies readings for one
measurement session. Modules run in the host process. Plugin selection and UI
implementation are outside this packet. Shutdown must reject new instances and
retire active instances before releasing module-global state.

## build.json

```json
{
  "product_modules": [
    {"name": "ruler", "path": "plugins/ruler.py", "kind": "core", "default_enabled": true},
    {"name": "camera", "path": "plugins/camera.py", "kind": "optional", "gate": "USE_CAMERA", "default_enabled": false}
  ],
  "examples": [
    {"name": "echo", "path": "examples/echo.py", "shipped": false}
  ]
}
```

## docs/extensions.md

Ruler is the built-in manual reading module. Camera is an optional SDK-based
module. Echo is an example for extension authors, not a shipped product module.
OnlineFX is a supported third-party extension protocol consumer, not bundled here.
Its source and compatibility tests are not included.

## host.py

```python
class Host:
    def __init__(self, modules):
        self.modules = modules
        self.instances = []
        self.readings = []
        self.accepting = True

    def create(self, name):
        if not self.accepting:
            raise RuntimeError("host stopping")
        instance = self.modules[name].create(self.readings.append)
        self.instances.append(instance)
        return instance

    def shutdown(self):
        self.accepting = False
        for instance in self.instances:
            instance.stop()
        for instance in self.instances:
            instance.destroy()
        self.instances.clear()
        for module in self.modules.values():
            module.unload()
```

## plugins/ruler.py

```python
class Ruler:
    def __init__(self, emit):
        self.emit = emit

    def read(self, value):
        if self.emit is not None:
            self.emit(value)

    def stop(self):
        self.emit = None

    def destroy(self):
        self.emit = None

def create(emit):
    return Ruler(emit)

def unload():
    pass
```

## plugins/camera.py

```python
import camera_sdk

context = camera_sdk.create_context()

class Camera:
    def __init__(self, emit):
        self.handle = camera_sdk.open(context, emit)

    def stop(self):
        camera_sdk.cancel(self.handle)

    def destroy(self):
        camera_sdk.release(self.handle)

def create(emit):
    return Camera(emit)

def unload():
    global context
    camera_sdk.release_context(context)
    context = None
```

## sdk/README.md

The camera SDK binary and API-contract documentation are not supplied. In
particular, this packet establishes no guarantee that `cancel` waits for callbacks
to finish. Camera callback threads and release-context requirements are unknown.

## tests/test_host.py

```python
import unittest
from types import SimpleNamespace
from host import Host

class HostTests(unittest.TestCase):
    def test_order_and_rejection(self):
        calls = []
        instance = SimpleNamespace(
            stop=lambda: calls.append("stop"),
            destroy=lambda: calls.append("destroy"),
        )
        module = SimpleNamespace(
            create=lambda emit: instance,
            unload=lambda: calls.append("unload"),
        )
        host = Host({"fake": module})
        host.create("fake")
        host.shutdown()
        self.assertEqual(calls, ["stop", "destroy", "unload"])
        with self.assertRaises(RuntimeError):
            host.create("fake")
```
