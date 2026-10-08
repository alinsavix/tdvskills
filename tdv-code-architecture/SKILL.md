---
name: tdv-code-architecture
description: Produce an evidence-grounded architecture guide covering purpose, domain concepts, components, contracts, invariants, flows, lifecycle, concurrency, data, deployment, trust boundaries, and testing across libraries, CLIs, clients, services, monorepos, and plugin/media applications. Invoke only with the explicit /tdv-code-architecture slash command. Analysis only unless the user requests files or changes.
disable-model-invocation: true
user-invocable: true
---

# Codebase Architecture Archaeology

Explain how a system works, where to make changes, and what those changes must not
break. Prefer source-grounded responsibilities, contracts, invariants and a few
end-to-end traces over a function-by-function encyclopedia.

## Invocation

Run only when the user explicitly invokes `/tdv-code-architecture`. Do not select
this skill automatically for natural-language architecture questions or other
inferred matches.

## Scope and defaults

- Analyze the requested repository or current repository; respect local changes.
- Do not alter source, configuration, branches, worktrees or submodule checkouts,
  initialize missing dependencies, or fetch artifacts merely to expand evidence.
- Default to analysis. Keep generated visual artifacts outside the repository
  unless the user requests project files. Saving a guide does not authorize a commit.
- Preserve unrelated files; do not overwrite different existing content without
  authorization. Saving an existing report need not repeat the investigation.
- Ask only when ambiguity materially affects the target, depth or destination.
- Research directly by default; size alone does not authorize delegation/workflows.
- This is not a debugging session, security audit, performance certification or
  permission to fix discovered problems.

## 1. Establish context, classify the system and choose depth

Start with repository instructions and the requested scope, then inspect relevant
root/build layout, README, manifests, configuration examples, CI and architecture
decision records (ADRs). A subsystem question does not require a repository-wide
inventory first. Record the revision, dirty scope, actual product-submodule
revisions versus parent pins, and missing/restricted evidence. Exclude generated/
vendor/build/cache trees unless a specific boundary requires them.

Before explaining implementation structure, establish a short system context:

- Who uses or calls the system, what they are trying to accomplish, and its
  boundary with external participants.
- The key domain terms, entities, relationships and state transitions needed to
  understand the selected operations. For a utility library, use consumer/input/
  output concepts rather than inventing a business domain or persistent state.
- Documented quality requirements and constraints that influence the design.
  Requirements and advertised capabilities are not measured achievements.

Ground this context in public contracts, examples, tests and product/design docs.
Distinguish stated purpose from implemented behavior; label inferred intent and
missing domain knowledge rather than constructing a plausible business story.

Identify one or more system shapes: library/framework, CLI, desktop/mobile/browser
client, service, distributed/event-driven system, batch/data pipeline, monorepo,
or plugin/native/media application. Do not force one shape onto a mixed repository.

Default to a **standard guide** for an unqualified architecture request. Use
targeted scope for a named subsystem or narrow question, and deep archaeology
when the request calls for detailed lifecycle/failure investigation. Depth controls
coverage, not verbosity: a brief whole-system guide can still be standard.
Match depth to the request without imposing an arbitrary file-count limit:

- **Targeted orientation:** requested subsystem, relevant boundaries, a key trace,
  invariants and starting files; explicitly exclude the rest.
- **Standard guide:** major responsibilities, execution/public entry points,
  candidate inventory, representative contracts/flows and meaningful exceptions.
- **Deep archaeology:** requested critical lifecycles, failure semantics and
  cross-boundary resources examined individually; report uninspected coverage.

Declare the depth, included systems/subsystems and relevant configurations,
external boundaries, exclusions, and representative operations before expanding
the investigation. All subsequent steps and checklists inherit that scope.
Expand it only when evidence exposes a material dependency or different contract
needed to explain the selected operations; record the reason for the expansion.

Use the following references only when applicable. Read selected sections rather
than all lenses by default; overlapping references supplement the common procedure.

| Shape or investigation need | Reference |
| --- | --- |
| Libraries/frameworks, CLIs, desktop/web/mobile clients, monorepos | [Application surfaces](references/application-surfaces.md) |
| Services, messaging, databases/caches, distributed or batch/data systems | [Distributed systems and data](references/distributed-data.md) |
| Product plugins, native/FFI ownership, realtime media and devices | [Plugins, native resources and media](references/plugins-native-media.md) |
| Configuration/deployment, observability, trust boundaries, testing/onboarding | [Operations and validation](references/operations-validation.md) |

**Exit criterion:** establish purpose/domain context, applicable shapes, depth and
bounded evidence scope. Libraries need public contracts and consumer behavior,
not artificial daemon loops.

## Evidence contract

- Cite significant claims with `relative/path:Symbol` or `relative/path:start-end`.
  Read relevant implementations; symbol presence alone proves no behavior.
- Mark **Confirmed**, **Inference** or **Unknown**. Section-wide Confirmed is fine
  if exceptions are individually qualified. "Not inspected" is not "does not exist".
- Distinguish declared support, build targets, packages, deployment declarations,
  runtime registration, default activation and observed operation.
- Verify indexed/remote source matches the analyzed checkout. Pin hosted source
  links to inspected revisions, including each submodule's own repository.
- Preserve contradictory evidence and stale docs. Intended architecture, current
  implementation and historical constraints may differ.
- Documentation/history can establish stated rationale, not implementation
  compliance. Inferred intent must remain Inference.
- Respect content exclusions and missing dependencies; never recover restricted
  contents another way. Inspect secrets references, never actual secret values.

## 2. Inventory candidates, then prioritize traces

Locate public APIs, application/runtime entries, jobs, helpers, OS-hosted modules,
plugin registrations, scripts, test harnesses and release/build targets relevant
to the selected scope. For each candidate record role, execution/consumer host,
relationship to product, feature/platform gates and investigation coverage.

Classify shipped/core/default-enabled, optional product, example/fixture/test,
user-loaded extension, external integration and merely supported third-party code.
Account for all discovered candidates in scope without treating every reference
in docs as a shipped component or recursively pursuing arbitrary third-party code.

Separate **inventory coverage** from **behavioral depth**: trace shared contracts
once, then critical components and meaningful deviations. Mark entries as
deeply traced, contract-mapped, inventory-only or unavailable, explaining important
gaps. Product plugins/submodules must not disappear because of directory placement.

Choose representative operations by architectural importance: central user/
consumer goals, authoritative state changes, shared critical paths, and important
failure or compatibility boundaries. Do not select only the easiest entry point
or replace a meaningful operation with a health check or trivial accessor.

**Exit criterion:** reconcile the candidate inventory with build/registration/
packaging declarations; justify deeper selections and exclusions.

### Investigation stopping rule

Stop expanding discovery when each major in-scope responsibility/boundary is
mapped or has a specific evidence gap, selected operations reach meaningful
completion/failure, and critical invariants and materially different contracts are
traced or explicitly qualified. Additional implementations that repeat a mapped
contract need inventory coverage, not another identical deep trace.

For remaining gaps, state the missing evidence, its consequence for the guide,
and the next useful investigation target. An unavailable critical boundary makes
that part incomplete; do not treat an Unknown label as proof or claim full
coverage. If evidence is available but essential work remains unfinished, continue
or plainly report the incomplete scope. Stop for coverage and diminishing new
information, not an arbitrary number of files or a requirement to eliminate every
unknown.

## 3. Map responsibilities, dependencies and invariants

Group components by runtime responsibility, not folders alone. For each major
component show responsibility, entry/public contract, state owner, dependencies,
and boundary interfaces. Distinguish build-time, control, data and process/network
edges; name serialization or compatibility contracts at important boundaries.

Identify dependency direction, layering, shared-state coupling, important cycles
and cross-cutting mechanisms. Do not confuse organizational ownership with runtime
ownership or infer a remote backend from an RPC adapter.

For each important invariant record:

`rule -> enforcing or relying code -> consequence if broken -> evidence status`

Examples include sole state ownership, allowed storage access, tenant scoping,
thread affinity, transaction/order constraints, resource lifetime and API/schema
compatibility. Distinguish enforced rules, documented expectations, assumptions
and legacy exceptions. Consult ADRs/design docs/history for constraints and
tradeoffs; do not invent design motives from code layout.

**Exit criterion:** explain who owns each responsibility and what a change must
preserve, including meaningful architectural compromises.

## 4. Trace operations through completion and failure

Select representative paths appropriate to the shape: consumer API/CLI command,
UI action/request, transformation/job/event, background work, extension invocation,
or capture-to-sink. Include initialization/termination where relevant and an
error/recovery/cancellation path; account for alternate contracts and exceptions.

Trace chronologically: trigger, handoffs, symbols, data transformations, auth/
authorization checks, state/storage/network effects, durable outcome, cancellation
and cleanup. Distinguish acceptance, execution, commit, publication, acknowledgment
and externally visible completion. For distributed paths, follow the logical
operation across process boundaries rather than stopping at a handler return.

**Exit criterion:** material steps are cited and completion/failure semantics
are explicit; unverified cross-repository behavior remains Unknown.

## 5. Establish lifecycle, concurrency and failure ownership

Trace significant resources as:

`creation -> ownership/retention -> use -> invalidation -> drain -> destruction`

Include stateful handles, callbacks/subscriptions, queues/workers, sockets/files/
database pools, native buffers/devices and plugin globals where present. Establish
strong/weak refs, GC/RAII, borrowed data, copy/ref APIs and explicit finalization.
Trace partial-init rollback and reload/disable separately from process exit.

Record actual ordering: reject work, cancel/drain, detach callbacks, join workers,
destroy instances/globals and invalidate host state. Identify reentrancy/
idempotence guards and bounded versus indefinite waits. A destroy hook, GC or
process exit does not prove callback quiescence or orderly shutdown.

Search explicitly for execution units: processes, threads, async tasks, workers,
timers, signals, callbacks, queues, actors, locks/channels and executors. Record
creator, resources, work input/output, synchronization/pressure, error propagation,
cancellation and lifetime. Distinguish application-owned units from opaque SDK/
framework workers; do not infer scheduling, thread count or lock freedom.

**Exit criterion:** relevant ownership and execution units are mapped or Unknown;
do not rewrite observed exit ordering into a cleaner hypothetical sequence.

## 6. Inspect data, operation, trust and test boundaries

Apply relevant lenses to persistent state/schema/migrations, transactions,
consistency/cache invalidation, message ordering/delivery/idempotency, and recovery.
Distinguish source components, build artifacts and deployed units. Examine
configuration precedence/flags, health/readiness, scaling/rollout/rollback,
failure domains, telemetry and recovery declarations without claiming production
topology from examples alone.

Map auth versus authorization, tenant/privilege boundaries, untrusted inputs and
sensitive-data movement as responsibilities, not a security certification.
Inspect unit/integration/contract/end-to-end test wiring, external substitutes,
fixtures and critical boundaries lacking evidence. CI compilation is not runtime
test coverage; tests do not certify all configurations or production behavior.

**Exit criterion:** applicable data/operational/trust/test boundaries are explained;
absent or out-of-scope concerns are not filled with invented architecture.

## 7. Cross-check and deliver a shape-appropriate guide

Cross-check source claims against configuration, build/package/deploy declarations,
tests and docs. Verify citations, revision links, counts, diagram edges and
inventory coverage; selectively reopen implementations for disputed or critical
claims rather than blindly rereading every citation.

Do not install tools, run builds, launch the product or exercise infrastructure/
devices merely for a source-level guide. Live validation requires user scope;
state precisely what was measured versus inspected.

Use a compact core report: purpose/actors/domain context, executive map,
snapshot/scope, responsibilities and boundary diagram, invariants/constraints,
representative flows, newcomer reading path, developer change map, and
evidence-backed unknowns. Define important domain terms before implementation
jargon; merge this context into the opening for small systems.

Add only relevant sections: public API/extension contracts; plugin inventory;
ownership/concurrency/exit order; data and messaging semantics; client/platform/
media seams; deployment/configuration/trust/observability; testing and recovery.
Merge related sections and omit inapplicable ones instead of imposing fourteen
headings on every repository. For a comprehensive guide, account for each selected
lens and significant uninspected boundary.

Give an ordered reading path with actual starting files and what each teaches.
Point to existing setup/build/run/debug/test instructions, qualify whether they
were verified, and identify prerequisites without executing them by default.
For common changes list entry file, coupled surfaces, invariants and relevant
tests. This answers both "where do I edit?" and "what must not break?"

Use Mermaid/ASCII or a requested visual/document artifact; invoke applicable
rendering skills before authoring their formats. Do not duplicate their styling
rules here. Keep large inventories in searchable/collapsible reference sections.

## Completion checklist

- Purpose, actors, system boundary and relevant domain concepts precede implementation detail.
- System shape/depth and candidate coverage are explicit, not universal deep-audit claims.
- The stopping rule is met within the declared scope, or incomplete coverage is stated.
- Snapshot, local differences and unavailable/restricted evidence are qualified.
- Responsibilities, dependency direction, contracts and invariants have evidence.
- Representative paths reach meaningful completion and cover failure/cancellation.
- Relevant data, deployment, trust, test and operational seams are accounted for.
- Ownership/lifecycle/concurrency claims reflect inspected code, not framework names.
- Specialized lenses retain their distinctions and plainly expose evidence gaps.
- Significant claims/citations/diagrams and declared versus observed support agree.
- Reading path and change map help a newcomer navigate and preserve constraints.
- Output matches scope and persists when requested; repository remains unchanged
  except for authorized report files.
- Visual artifacts render with working links/interactions and no desktop/mobile
  overflow; terminate temporary validation processes afterward.

## Maintaining this skill

When changing the skill, use the [behavioral acceptance scenarios](evals/README.md)
to check generated guides, not just metadata and keyword presence. This is a
maintenance check, not an extra step for ordinary architecture requests.
