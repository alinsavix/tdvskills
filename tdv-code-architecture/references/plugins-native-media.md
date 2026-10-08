# Plugins, Native Resources, Realtime Media and Devices

Use the relevant sections; a plugin system need not be native or multimedia.
Preserve the core distinction between inventory coverage and depth of investigation.

## Plugin candidate coverage and prioritization

- Discover candidates from shipped/bundled/default-enabled modules and direct
  product/build/package/config/docs/test references.
- Classify product modules, optional product modules, extra generated binaries,
  examples/fixtures/tests, user-loaded scripts and merely supported external code.
- Record inclusion rationale, host/registration entry, role, feature/platform
  gates and investigation coverage for every discovered in-scope candidate.
- Trace shared host contracts once. Investigate representatives and deviations
  involving native resources, callbacks, thread/process boundaries, cross-plugin
  state, compatibility, failures or different disable/unload behavior.
- For contract-mapped modules identify actual create/destroy/unload wiring or
  a gap; inventory-only entries must not be presented as lifecycle-audited.
- Do not omit shipped modules because they live in plugins/ or a submodule, and
  do not chase every external plugin mentioned in documentation.

## Host integration and lifecycle

- Identify discovery/search paths, manifest/export loading, dependency resolution,
  ABI/API compatibility, registration tables and post-load coordination.
- Trace a host -> plugin -> host interaction and failure/partial-init cleanup.
  Distinguish type registration, instance construction, activation and readiness.
- Establish any trust/capability/sandbox/permission boundary. Dynamic loading is
  not process isolation, and manifests alone do not prove dependency resolution.
- Trace disable/reload/unload independently from whole-process shutdown. Determine
  restart requirements, new-work rejection, callback detachment, instance/global
  destruction, task drain and code/library lifetime.
- Preserve exact ordering. Loaded code can outlive invalid plugin globals;
  module unloading can precede forced cleanup of retained instances.
- Record idempotence/reentrancy, exception/error paths, timeout/forced-exit behavior
  and SDK assumptions. A successful load hook does not prove all instances work.

## Native and cross-boundary resources

- Trace host handles, callbacks/subscriptions, device/SDK state, frames/textures/
  sample buffers, sockets, file/database handles, workers and queued work.
- Establish strong/weak references, retain/release, RAII/GC boundaries, explicit
  finalizers, borrowed callback buffers, copy/ref APIs and pool recycling.
- Follow native-handle transfer across FFI/process boundaries, including success,
  failed transfer, queue-full and cancellation paths.
- Check which context/thread owns destruction and whether work can outlive its
  creator. SDK stop/revoke calls and GC do not by themselves prove quiescence.
- Expose missing device/SDK implementation evidence without asserting a leak or
  use-after-release solely because a safety proof is incomplete.

## Media pipelines

- Trace capture/playback -> source/model -> processing/composition -> encoding or
  raw delivery -> each materially different sink, reusing shared contracts.
- Separate async CPU frames, shared/native textures, custom rendering, CPU/GPU
  encoding, preview/edit views, program output, and raw camera/hardware output.
- Include audio conversion/resampling/filtering/mixing, track selection,
  monitoring and deduplication where present; monitoring may be a separate path.
- Distinguish capture/decode/presentation timestamps, monotonic/wall time, audio
  sample clocks, encoder PTS/DTS/timebases and transport clocks.
- Identify conversion, buffer/ref ownership, capacity, latency settings,
  drops/repeats, jitter/drift correction and reconnect behavior.
- Treat SDK/capture callbacks as deadline-sensitive until evidence shows otherwise.
  Inspect allocation, locks and I/O on hot paths; FPS intervals and queue limits
  are not measured end-to-end latency or realtime-safety guarantees.
- Include profiler/metrics locations without claiming a live workload was profiled.

## Device and platform integration

- Trace enumeration, permissions, capability/mode negotiation, device opening,
  media callbacks, hotplug, retry/reconnect and teardown.
- An SDK import or detected adapter is not proof of a usable hot-path device.
- Distinguish compile-time/platform/architecture flags, runtime API/driver checks,
  packaging-only components and selected default versus deprecated adapters.
- Inventory helper probes, browser/media subprocesses, injected libraries and
  OS-hosted extensions as separate execution hosts. Probe watchdog exits do not
  imply bounded whole-application shutdown.
- Qualify device compatibility, native callback-drain guarantees, clock drift and
  latency as Unknown unless supported by the relevant evidence or measurements.

## Delivery check

Provide candidate coverage, host contracts, selected deep traces, native ownership,
execution contexts and observed exit ordering. Include media/device sections only
when relevant; keep uninspected modules and runtime assumptions visible.
