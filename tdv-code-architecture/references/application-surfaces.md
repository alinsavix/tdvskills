# Application and Consumer Surfaces

Use only the sections matching the repository. Apply the core evidence/coverage
rules: identify real contracts and observable behavior, not a framework stereotype.

## Libraries and frameworks

- Find public exports/headers, package entry modules, factories, context/builders,
  registration points and integration hooks. Separate public API, supported
  extension/SPI surface, private internals and generated bindings.
- Explain how a consumer constructs/configures the library, invokes a typical
  operation, receives results/errors and releases resources. A stateless library
  need not have startup/shutdown or a steady-state loop.
- Map input/output types, pre/postconditions, mutation, callback/thread-affinity
  rules, reentrancy, cancellation and ownership. Inspect synchronous versus lazy/
  streaming results and exceptions/error codes.
- Trace one public operation through transformations/algorithms and dependencies;
  framework repositories may be entered through registration/inversion of control.
- Verify API/ABI, version/serialization and compatibility promises against actual
  export/build/test evidence. SemVer naming alone does not prove compatibility.
- Identify dependency boundaries, portability assumptions, performance constraints
  and invariants. Do not invent deployable services around a reusable package.
- Reading path: public surface -> minimal consumer/example -> main implementation
  -> contract tests -> extension/compatibility documentation.

## CLIs and developer tools

- Map launch/dispatch, command hierarchy, parser/defaults, configuration precedence,
  stdin/stdout/stderr, exit codes, signals and error reporting.
- Trace a representative command to its final file/process/network side effects.
  Distinguish dry run, interactive prompts, machine-readable output and daemon/
  watch modes; describe finite termination where there is no event loop.
- Inspect shell/subprocess adapters, working-directory/path policy, environment,
  temporary files and cleanup. Explain who owns child cancellation and exit status.
- Include plugins/config loading only when actually present.
- Reading path: main/entry -> command dispatch -> command implementation -> output/
  error contract -> command/integration tests.

## Desktop and native applications

- Map application shell, UI state ownership, events/actions, in-process engine/
  model boundaries, frontend APIs and OS-specific adapters.
- Trace a UI action and its actual completion; identify event queues, thread
  marshaling, modal/nested loops and hidden retained resources where evidenced.
- Distinguish display/preview state from persisted/program/background state.
- Trace shutdown, session termination and background/child-process ownership;
  use the native-resource lens for significant callbacks or FFI handles.
- Reading path: entry -> application/main-window initialization -> one action ->
  model/engine contract -> persistence and lifecycle -> representative tests.

## Browser applications

- Map browser bootstrap, route/navigation entry points, state stores, component
  ownership, rendering effects, forms and transport clients.
- Separate client, server-rendered and hydration boundaries; service workers,
  workers and backend-for-frontend are separate hosts if actually configured.
- Trace request state, auth/session handling, authorization enforcement location,
  caching/invalidation, optimistic updates, rollback and error presentation.
- Inspect state lifetime, stale requests, subscription/effect cleanup, navigation
  cancellation and background synchronization.
- Map assets/build entry points, code splitting and release hosting declarations.
  Frontend validation or hidden UI is not a server authorization mechanism.
- Reading path: bootstrap/routes -> key screen/action -> store/client -> service
  contract -> error/cache logic -> component/contract/end-to-end tests.

## Mobile clients

- Map application/activity/scene entry, navigation, model/store ownership, platform
  services, app extensions and lifecycle/background-task rules.
- Trace foreground/background transitions, restoration, permissions, offline
  storage/synchronization, interrupted operations and process recreation.
- Identify native bridges, push/deep-link inputs, OS-managed scheduling and which
  behavior depends on platform contracts rather than repository-owned workers.
- Treat simulator/source support and real-device behavior as different evidence.
- Reading path: launch/navigation -> feature -> model/storage/network -> platform
  integration/lifecycle -> tests and packaging/signing declarations.

## Monorepos and mixed systems

- Discover workspace/build definitions, package/project graph and independently
  shipped/deployed units. Classify each unit rather than inventing one global app.
- Separate shared packages/tooling, product applications, examples and fixtures.
  Shared source does not necessarily imply a shared deployment or runtime state.
- Map internal API/schema/version coupling, generated artifacts and dependency
  direction/cycles. Identify cross-package build/release/test impact where evidenced.
- Trace one significant operation across packages, qualifying missing external
  repositories. Do not recursively expand every dependency into a deep audit.
- Reading path: workspace graph -> relevant product entry -> boundary contract ->
  producer/consumer packages -> their tests and build/release integration.

## Delivery check

Choose the matching entry/consumer inventory and reading path. Include the real
frontend/backend/platform split, or explain why that split does not apply.
Critical contracts and constraints belong in the core report; detailed package/
command/screen inventories can remain reference material.
