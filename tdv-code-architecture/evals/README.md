# Behavioral Acceptance Scenarios

Use these scenarios when maintaining `tdv-code-architecture`, not during every
architecture request. They evaluate generated explanations against concrete
evidence and user scope rather than checking whether the skill mentions keywords.

## Contents and limits

[cases.json](cases.json) contains five prompts, expected depths, required findings,
prohibited claims, and shared acceptance criteria. The referenced evidence packets
contain synthetic source snapshots for a library, CLI, dirty monorepo, message
consumer, and plugin host. Each packet explains its evidence boundary.

These are source-analysis fixtures, not runnable applications. Their embedded
paths belong to the fictional repositories, not the surrounding checkout.
Snapshot metadata and diffs are supplied evidence, not real local Git state.
Do not run embedded commands, initialize repositories, install dependencies, or
contact external services to evaluate them.

Packet evaluation tests scope selection, domain explanation, evidence reasoning,
flow selection, and honest uncertainty. It does not test repository discovery,
actual Git inspection, tool routing, production behavior, or visual rendering.
A passing packet run is not proof of broad real-repository performance.

If the skill loader has not discovered the personal skill or its local edits, use
the supplied `SKILL.md` file directly and record discovery as a separate issue.
A file-based evaluation does not validate loader registration. Route any required
helper/friction logs to the artifact directory too, not the analyzed repository.

## Procedure

1. Record the skill/reference/fixture revisions or content hashes, model identity,
   evaluation date, and any generator settings actually known. Keep reports and
   results in a session/artifact directory, not in an analyzed project.
2. Give a generator the current `SKILL.md`, access to its applicable references,
   the explicit `/tdv-code-architecture` command followed by the case's exact
   `prompt`, and the packet named by `evidence`. State that the packet is the
   complete supplied evidence and all other implementation details are
   unavailable. Request plain Markdown, not HTML or a live demonstration.
3. Keep `common_required`, `common_forbidden`, and per-case `required`/`forbidden`
   criteria out of the generation context. Use fresh contexts per case when
   practical; if batching cases or doing a self-review, record that limitation.
   Agent/workflow use still requires the surrounding environment's authorization.
4. Save each guide before scoring it. Grade the guide semantically against every
   shared and case-specific criterion. Cite the report passage and fixture source
   supporting each verdict; for an omission, identify what was missing. Keyword
   presence alone is not a pass, and cautionary mentions are not forbidden claims.
5. Record `pass`, `fail`, or `unassessed` for each criterion. `unassessed` is not a
   pass. A case passes only when every required behavior is demonstrated and no
   prohibited behavior is asserted. Honor an explicit `max_words` using
   whitespace-delimited words; headings, citations and code count toward it.
6. Keep first-pass failures. After a targeted skill revision, generate a new
   report rather than editing an old one to fit the rubric. Re-run affected cases
   and scope/read-only regression checks. Report remaining failures and limits.

Expected findings are not a required report template. Equivalent accurate wording
is acceptable; small systems should remain small reports. Unknowns must identify
real gaps, not replace available evidence. Evaluate task actions from a tool trace
if one exists; report-only scoring cannot establish that no side effects occurred.

## Invocation routing checks

These require the actual skill loader, not a direct-file packet evaluation.
After renaming or changing invocation settings, use `/skills reload` in the
interactive CLI and check that:

- `/tdv-code-architecture` is available for explicit user invocation.
- Natural-language architecture requests do not automatically select this skill.
- The former `/codebase-architecture` command is no longer registered.

Keep `disable-model-invocation: true` and `user-invocable: true`. If the loader
blocks explicit invocation, record that as a routing failure; do not re-enable
automatic invocation to make the test pass.

## Result record

For each run preserve:

- Skill/reference/fixture hashes, model/date, generation setup and limitations.
- Case ID, exact prompt, report path and word count when bounded.
- A verdict for every criterion ID, with report/fixture evidence or omission.
- Overall case status and any corrective action or intentionally deferred issue.

Do not call a structural manifest/link check a behavioral run. New scenarios
should capture a distinct failure mode, not merely add another framework name.
