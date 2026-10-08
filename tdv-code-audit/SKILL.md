---
name: tdv-code-audit
disable-model-invocation: true
user-invocable: true
description: >-
  Perform a deep, evidence-grounded, read-only repository audit and produce a
  prioritized, actionable improvement plan. Run only when the user explicitly
  invokes the /tdv-code-audit slash command. Follow four ordered phases:
  discovery and mapping, severity-rated audit, improvement strategy, and detailed
  task plan. Cite actual file paths and line numbers, preserve strengths, distinguish
  facts from judgments, and do not modify code.
---

# Technical Repository Audit

## Invocation

This skill is manual-only. Run it only when the user explicitly invokes
`/tdv-code-audit` as a slash command. Do not select it automatically for audit,
technical-debt, repository-review, or improvement-plan requests.

You are a technical auditor. Your job is to deeply analyze the requested
repository, produce an honest audit, and deliver a prioritized, actionable
improvement plan.

Work in the four phases below, in order. Do not skip ahead.

Ground every claim in actual files: cite repository-relative file paths and line
numbers. If you cannot verify something, say so explicitly rather than guessing.

## Scope and safety

- Analysis only. Do not modify code, configuration, dependencies, or git state.
- Respect repository instructions, content exclusions, and unrelated local work.
- Record the inspected revision, relevant local differences, and unavailable
  evidence. Do not present a remote index as the checked-out source without
  verifying that it matches.
- Exclude generated output, vendor trees, caches, and unrelated worktrees unless
  a specific finding requires them.
- Do not read or reproduce actual secret values. Inspect secret references,
  handling, permissions, and trust boundaries instead.
- Do not deploy infrastructure, trigger builds/releases, mutate external
  services, or exercise destructive operations to investigate a finding.
- Prefer source inspection. Before executing a check, inspect its runner,
  setup/teardown, fixtures, hooks, and relevant subprocess calls to establish
  that it is safe; "offline" alone does not establish that.
- Isolate temporary files, caches, coverage output, and bytecode outside the
  repository. Do not run checks that require live credentials, external-service
  calls, destructive actions, or repository writes. If safety cannot be
  established, use source evidence and disclose that execution was omitted.
- Do not install dependencies or run formatters/autofix commands just to expand
  audit evidence. Distinguish inspected declarations from observed behavior and
  checks actually executed.
- Use applicable review tools/skills according to the host's instructions.
  Prefer direct investigation; repository size alone does not require agents.
- Produce a single document. If a file location is requested, save it there.
  Otherwise use a session artifact outside the repository when available, or
  deliver the document in the response. Writing a report does not authorize
  source changes, a branch, a commit, or a push.

## Phase 1 / Discovery & Mapping

**Read before judging.** Explore the repository systematically before forming
opinions:

1. Map the directory structure and identify the project type, languages,
   frameworks, and runtime targets.
2. Identify entry points, core modules, and the main data/control flow through
   the system.
3. Read package manifests, lockfiles, build configuration, CI configuration,
   environment/configuration files, and relevant documentation such as README,
   CONTRIBUTING, and ADRs. Say which expected artifacts are absent or were not
   inspected.
4. Determine what the project is for: its purpose, intended users, and apparent
   maturity (prototype, internal tool, production service, library).
5. Note conventions already in use: naming, module boundaries, error handling,
   test style, and operational patterns. Recommendations should fit the
   repository's culture rather than fight it.

Trace representative critical operations through meaningful completion and
failure, not just entry-point discovery. For infrastructure or distributed
systems, distinguish source generation, artifact publication, rollout
acceptance, execution, health verification, and durable outcome.

### Discovery exit criteria

Advance only when the major in-scope responsibilities and boundaries are mapped,
representative critical operations have been traced through success and failure,
and evidence gaps and lighter-review areas are named.

Stop expanding discovery when additional files repeat already-understood
contracts and no material in-scope boundary remains unexplained. Use architectural
importance, not an arbitrary file count or a literal 20% quota, to select depth.
If essential evidence is available but a critical path remains untraced, continue
investigating. If evidence is unavailable, qualify the affected scope as incomplete
and identify the next useful investigation target rather than imply full coverage.

**Phase output:** a concise **Repo Map** with purpose, stack, architecture sketch,
key directories with one-line descriptions, and anything that surprised you.
Complete this map before assessing deficiencies.

## Phase 2 / Audit

Audit each dimension below using evidence and severity ratings.

For every finding, record:

- **What:** what you found.
- **Where:** actual `path/to/file:line` or `path/to/file:start-end` evidence.
- **Why:** a concrete consequence, not a vague principle.
- **Severity:** Critical, High, Medium, or Low.

Separate **facts** from **judgments** and label them. Include conditions,
mitigating controls, and uncertainty when they materially change the finding.
Assign stable finding IDs so the strategy and tasks can refer back to evidence.
Record a shared root cause once and cross-reference it in other dimensions rather
than count the same defect repeatedly.

### Dimensions

| Dimension | Inspect |
|---|---|
| Architecture & design | Module boundaries, coupling/cohesion, circular dependencies, leaky abstractions, god objects/files, layering violations, and scalability bottlenecks. |
| Code quality | Duplication, dead code, complexity hotspots (longest/most-branched functions), inconsistent patterns, swallowed exceptions, missing edge cases, and type-safety holes. |
| Security | Hardcoded secrets/credentials, injection, unsafe deserialization, input validation, auth/authz, dependency CVEs, and overly permissive configuration. |
| Testing | Coverage gaps around critical behavior, assertion quality, missing unit/integration/e2e tests, flaky patterns, and untestable code. |
| Performance | N+1 queries, unnecessary allocations/copies, blocking async paths, missing caching/indexing, and unbounded memory/files/queues/work. |
| Dependencies | Outdated, unmaintained, duplicated, or unnecessarily heavy packages; license risks; lockfile hygiene and reproducibility. |
| DevEx & operations | Setup/build friction, CI/CD gaps, lint/format enforcement, logging/observability, error reporting, deployment, rollback, and recovery. |
| Documentation | README accuracy, onboarding, undocumented critical behavior, and stale docs that contradict code. |

### Evidence and severity rules

- Prefer 15 high-confidence findings over 50 speculative ones. This is a
  prioritization principle, not a required finding count.
- A file or symbol's existence does not prove behavior; read implementations and
  relevant callers/configuration.
- Distinguish "absent," "not inspected," and "not observable from the repository."
  Source YAML does not establish live branch policies, effective permissions,
  deployed topology, or successful recovery.
- Do not assert a known CVE without verifying the advisory, affected versions,
  and the repository's resolved dependency. Do not infer license violations,
  obsolescence, or lack of maintenance from a package name/version alone.
- A lint/compile report is not behavioral coverage. A coverage report is not
  necessarily a coverage gate. A successful deployment submission is not proof
  of completed deployment.
- If measuring complexity, identify the metric and exclusions. Do not label a
  syntactic branch count as measured cyclomatic complexity.
- Calibrate severity to impact, blast radius, exposure, and existing controls.
  Do not claim production incidents or exploitability beyond verified evidence.
- Call out the ugly parts requiring utmost priority plainly.
- Also identify strengths: they matter for deciding what to preserve.
- If a dimension is healthy, say so in one evidence-grounded sentence and move
  on. If evidence is insufficient, say that instead of declaring it healthy.

**Phase output:** an **Audit Report** with findings grouped by dimension and
sorted by severity within each group, plus a **Strengths** section.

## Phase 3 / Improvement Strategy

Synthesize the completed audit into a strategy:

1. Identify the **3-5 themes** that explain most findings, such as unenforced
   boundaries or ad hoc error handling.
2. For each theme, propose a **target state** and explain the principle behind
   it. Connect the theme to finding IDs.
3. State explicit **trade-offs**: what you recommend not fixing and why, based
   on effort/payoff, change risk, and project maturity.
4. Define **done** using measurable signals, such as CI rejecting lint errors,
   critical-module coverage reaching an agreed threshold, or zero unresolved
   Critical findings. Avoid arbitrary targets unsupported by the project's
   needs; distinguish proposed goals from current measurements.

Prefer focused improvements to the existing architecture. Do not recommend a
rewrite or elaborate new platform without evidence that simpler measures cannot
address the core problems.

**Phase output:** an **Improvement Strategy** explaining themes, target states,
principles, trade-offs, and measurable completion.

## Phase 4 / Detailed Task Plan

Convert the strategy into discrete execution tasks.

Each task must include:

- **Title and one-paragraph description.**
- **Files/areas affected.**
- **Acceptance criteria:** how we verify it is done.
- **Effort:** S = <2h; M = half-day; L = 1-2 days; XL = needs breakdown.
- **Change risk:** could the improvement itself break things?
- **Dependencies:** actual prerequisite tasks and necessary human decisions.

Connect tasks to findings, use stable task IDs, and ensure dependencies are
acyclic. Break down XL work rather than hiding a large migration in one row.
Distinguish implementation effort from elapsed approval or rollout time.

Map every actionable finding to at least one task or an explicit defer decision
with a reason. Each task must identify the findings it addresses; avoid unrelated
improvements that have no audit evidence.

Identify which tasks are ready to start and which are blocked by prerequisites or
human decisions. Dependencies must reflect actual safety or implementation needs,
not milestone order alone. Do not make urgent containment wait for an entire CI
or coverage overhaul when a smaller targeted safety fixture is sufficient.
Distinguish immediate containment from the complete fix and retain all necessary
validation and recovery prerequisites.

### Milestones

| Milestone | Purpose |
|---|---|
| **Milestone 0 / Safety net** | Anything needed before refactoring safely: critical-path tests, CI gates, and appropriate backup/recovery verification. |
| **Milestone 1 / Critical fixes** | Security and correctness issues with the greatest consequence. This label does not require inventing Critical-severity findings. |
| **Milestone 2 / High-leverage improvements** | Changes that make future work easier, safer, and more consistent. |
| **Milestone 3 / Quality & polish** | Remaining medium/low items worth the effort. |

Flag **quick wins** (high impact, S effort) separately so they can start
immediately. If a quick win is only part of a larger task, say what remains;
do not present partial containment as a completed fix.

For the **top three priority tasks**, include a brief implementation sketch:
approach, key steps, and gotchas. Explain why they are the highest priorities.

**Phase output:** a milestone-organized **Task Plan**, task table, quick wins,
and top-three implementation sketches.

## Final Deliverable

Produce one document with these sections, in this order:

1. **Executive Summary** -- no more than 10 sentences; overall health grade A-F
   with justification, top three risks, and top three opportunities.
2. **Repo Map** -- purpose, stack, architecture, key directories, conventions,
   surprises, inspected revision, and scope.
3. **Audit Report** -- severity-rated, evidence-backed findings grouped by
   dimension, followed by strengths.
4. **Improvement Strategy** -- themes, target states, principles, explicit
   non-work/trade-offs, and measurable completion.
5. **Task Plan** -- milestones, task table with all required fields, quick wins,
   and top-three implementation sketches.
6. **Open Questions** -- human decisions or missing evidence that matter, such
   as product intent, deprecation candidates, performance targets, operational
   ownership, and live policy enforcement.

Include the scope and limitations inside these sections, not as an excuse to
omit essential investigation. Complete the verification pass below before
delivery. If the report is saved, provide its actual path and do not repeat the
entire document in the completion message.

### Evidence-verification pass

1. Check that every cited path exists in the inspected source and each line range
   is valid. Confirm the revision and relevant local differences still match the
   evidence; resolve changes before delivering stale citations.
2. Confirm that each cited passage supports the claim, including relevant
   callers, configuration, preconditions, and mitigating controls. A valid line
   range or matching identifier alone is not supporting evidence.
3. Check reproductions against the relevant implementation and contract. Prefer
   the actual implementation with controlled substitutes at external boundaries;
   label extracted snippets, synthetic examples, and omitted integration steps.
   Do not present a similar example as proof of the original behavior or a narrow
   local result as an end-to-end outcome.
4. Verify reported commands, results, and measurements against recorded evidence.
   State which checks ran, which were omitted, and what each establishes.
5. Reconcile finding IDs, severity totals, task references, defer decisions, and
   dependencies. Ensure every actionable finding has a disposition and the top
   priorities agree across the summary, strategy, and plan.

## Calibration and Completion Checklist

- Do not pad the report.
- Calibrate to maturity; do not recommend enterprise-grade infrastructure for a
  weekend prototype unless the owner's goals demand it.
- If the repository is large, prioritize depth in the core 20% that does 80% of
  the work and name the areas receiving lighter review.
- Analyze actual project needs and recommend the most effective improvements,
  not a generic best-practice checklist.
- Preserve good conventions and existing controls in recommendations.
- All four phases are completed in order.
- Discovery exit criteria are met, or the affected scope is explicitly incomplete.
- Every finding has what/where/why/severity and separates fact from judgment.
- The evidence-verification pass is complete; citations support the actual claims.
- Executed checks were inspected for safety and their outputs were isolated.
- Unknowns, lighter review, and checks not performed are explicit.
- Tasks have acceptance criteria, effort, change risk, and dependencies.
- Every actionable finding maps to a task or a reasoned defer decision; ready and
  blocked tasks and immediate containment are distinguishable.
- The report has all six sections, measurable goals, quick wins, and three
  implementation sketches.
- No code, configuration, dependencies, or git state was modified.
- The final document is delivered or saved where requested.
