# Operations, Trust Boundaries, Testing and Onboarding

Apply the relevant concerns to the actual system shape. A library may have package/
compatibility/test concerns but no deployment or readiness probes. Use declarations
and source safely; live environments require separate user authorization.

## Artifacts and deployment topology

- Map source components -> build artifacts -> configured execution/deployment
  units. Distinguish independent services/workers, helper processes, client
  bundles, packages, extensions and CI jobs.
- Inspect manifests, infrastructure/deploy definitions and launch configuration.
  Label templates/examples, build defaults, environment overrides and obsolete
  definitions rather than claiming they describe production.
- Identify replicas/partitions, placement, external dependencies, failure domains,
  stateful versus stateless scaling and leadership/lease assumptions.
- Map release dependencies, feature rollout, schema/API mixed-version constraints,
  drain/termination, rollback and recovery declarations.
- A green compile job does not prove successful deployment or live capabilities.

## Configuration and feature policy

- Trace precedence from defaults through files, environment, CLI and remote
  configuration where present; identify validation and invalid-input behavior.
- Separate build-time flags, startup configuration, runtime feature decisions,
  per-user/tenant policy and packaging-only conditions.
- Record change semantics: immediate, reload/restart/rebuild, cache refresh or
  migration; trace the actual consuming code, not just option definitions.
- Map secret provider/reference/injection boundaries without reading values,
  dumping environments, user configs or production credentials.
- Identify architectural assumptions encoded in configuration and drift risks
  between examples, deployment manifests and consuming code.

## Operational behavior and observability

- Map liveness, readiness and startup health semantics: what each check verifies,
  what dependencies it probes and what state admits new work.
- Trace failure/error propagation, timeout/retry/circuit breaking, degradation,
  saturation/backpressure, shutdown/drain and operator recovery entry points.
- Identify logs, metrics, traces/profiler hooks, correlation/context propagation,
  counters and documented alerts/runbooks for important boundaries.
- Distinguish instrumented behavior from available dashboards/active alerts and
  measured service objectives. Do not invent operational coverage from imports.
- Trace declarations for backup/restore, replay/reconciliation or disaster
  recovery where present; declaration existence does not prove recovery was tested.

## Trust and privilege boundaries

- Identify user/process/service identities, authentication/session establishment,
  authorization decision/enforcement points and propagated identity context.
- Map tenant isolation, object/resource scoping, privileged operations, admin/
  extension capabilities and trust changes across process/network/FFI boundaries.
- Follow untrusted inputs to validation/interpretation and sensitive-data movement
  across stores, caches, APIs, queues, telemetry and external integrations.
- Separate UI restrictions from server enforcement, authentication from
  authorization, and in-process plugin capability from actual isolation.
- Describe mechanisms and architectural responsibility with C/I/U evidence.
  This is not a vulnerability search, exploit attempt or security certification.
  If an explicit security review is requested, use its applicable workflow.

## Testing architecture and evidence

- Map unit, component/integration, contract, end-to-end, migration/compatibility,
  recovery/concurrency and platform/hardware tests where present.
- Trace actual runner/build/CI wiring, discovery/selectors and required services/
  fixtures. A tests/ directory or helper named "test" proves no executed suite.
- Identify boundaries exercised, external dependencies mocked/emulated/real, and
  fidelity gaps. Passing a mocked contract does not prove provider behavior.
- Connect important invariants to enforcement and tests; report missing evidence
  for critical seams without declaring uninspected tests absent.
- Distinguish declared test coverage, inspected assertions and executed results.
  Architecture analysis does not authorize infrastructure provisioning or test runs.
- Cite established commands/docs and qualify whether current wiring supports them;
  do not prescribe guessed build/test commands as verified.

## Newcomer reading path and developer change map

- Provide a short ordered path of actual files/resources and what each teaches:
  purpose/domain context -> boundary/public entry -> representative operation ->
  state/contracts -> failure/lifecycle -> tests/configuration, adapted to the
  system shape. Context can come from the public API/examples for a small library.
- Link existing setup/build/run/debug/test instructions and prerequisites.
  Mark stale/unverified commands and missing dependency/environment information;
  do not install tooling or run the application by default.
- For common changes map starting file, coupled producers/consumers, invariants,
  compatibility/platform implications and relevant test suites.
- Include architectural constraints/tradeoffs with ADR/design/history evidence.
  Label inferred motives, legacy exceptions and documented-but-unenforced rules.
- Give unresolved questions precise evidence gaps and next investigation targets,
  not a generic invitation to "review more code".

## Delivery check

Include only applicable operational/trust/test concerns. Keep deployment facts
qualified, do not expose secrets, and make the reading path useful without turning
the guide into a deployment runbook or a security/performance certification.
