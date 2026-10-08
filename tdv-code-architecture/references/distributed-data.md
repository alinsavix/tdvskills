# Distributed Systems, Messaging and Data

Read the applicable sections for services, stateful clients, event-driven systems,
database-backed applications and batch/stream pipelines. Do not introduce
distributed semantics into a purely local stateless library.

## Services and process boundaries

- Map listener/handler/middleware entry points, orchestration, domain logic,
  storage/adapters, worker processes and independently deployable units.
- Record concrete interface contracts: transport/protocol, serialization, version,
  identity context, request/event types, deadlines and errors.
- Label network, process, control and data edges separately. Multiple source
  packages or a container recipe do not prove multiple production services.
- Trace one logical operation across boundaries to its durable/externally visible
  outcome, and a failure path. Identify propagation of deadlines/cancellation/
  identity and responsibility for compensation or retry.
- Qualify unseen service/SDK implementations; client call sites only establish
  local behavior and assumptions, not the remote service's internal guarantees.

## State ownership, schema and evolution

- Identify authoritative stores versus caches, derived views, local replicas and
  temporary state. Name which component is permitted to mutate each resource.
- Locate schema definitions, migrations, query/repository layers, serialization
  contracts, indexes/constraints and version negotiation.
- Trace actual read/write paths and data transformations, including tenant and
  identity scope. A database import is not proof of the authoritative store.
- Inspect migration application/ordering, backward/forward compatibility, mixed
  versions, rollback limitations and retained historical formats.
- Include retention/deletion, sensitive-data classification and backup/restore
  behavior where declared; do not inspect production data or credentials.

## Transactions and consistency

- Show transaction boundaries, commit timing, isolation/concurrency assumptions,
  constraints/locks and failure rollback. Distinguish DB transaction scope from a
  larger logical operation spanning other stores or remote side effects.
- Trace lost-update prevention, conflict/version checks and reads from stale
  replicas where present. Do not claim atomic cross-service effects from local code.
- For database plus message/remote effects, look for outbox/inbox, compensation,
  reconciliation, sagas or explicitly exposed gaps; do not assume those patterns.
- Document state-machine invariants and enforcement: allowed transitions, sole
  writers, uniqueness, ordering and completion markers.
- Evidence should distinguish durable commit, publication, acknowledgment and
  externally observable completion.

## Caches and derived state

- Locate keys, scopes, TTLs, eviction, invalidation/update triggers and authoritative
  source. Distinguish local/distributed caches and stale-read behavior.
- Trace misses, concurrent fills, error results, negative caches and recovery from
  inconsistent derived views. A TTL alone does not explain correctness.
- Record invalidation ownership and important coherence assumptions; identify
  whether tenant/user-sensitive state shares a cache boundary.

## Messaging, jobs and recovery

- Map producers/consumers, topics/queues, scheduling, acknowledgment/offset commit,
  partitions and ordering scope. Inspect actual configuration and adapter code.
- Distinguish declared delivery guarantees from end-to-end effects. Broker
  "exactly once" terminology does not prove side effects happen exactly once.
- Trace retry triggers/limits/backoff/jitter, deadlines, idempotency keys,
  deduplication persistence/expiry and poison-message/dead-letter handling.
- Include crash windows around write/send/ack, redelivery, duplicates, replay,
  reconciliation and partial completion. Ask which component owns recovery.
- Map concurrency, worker leases/leadership, partition assignment, queue limits,
  admission control/backpressure and cancellation/drain semantics.
- Logs/tests proving a happy path do not establish crash-safe recovery.

## Batch, ETL and stream-processing systems

- Map inputs, parser/validation, transformation stages, partition/shuffle,
  materialization and sinks, with actual execution entry/scheduler.
- Identify incremental/full modes, lineage, schemas, checkpoints, resume markers,
  watermark/event-time versus processing-time and late/out-of-order handling.
- Trace an item/partition/job to sink commit, plus retry/restart. Distinguish
  per-record acceptance, stage completion and whole-job durability.
- Inspect partial output cleanup, reprocessing/backfill, idempotent sinks,
  deduplication, data-quality checks and resource/backpressure bounds.
- Treat notebooks or local examples as separate from established scheduled jobs.
- Reading path: job/service entry -> contract/schema -> main transformation or
  orchestration -> commit/ack/checkpoint -> recovery -> representative tests.

## Delivery check

Include a state-owner map, boundary contracts and relevant transaction/event
sequence. Explain invariants, compatibility and failure windows with evidence.
Mark guarantees dependent on missing infrastructure/runtime configuration Unknown;
do not silently promote configuration examples to observed production behavior.
