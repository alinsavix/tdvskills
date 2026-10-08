# TDV Skills

Evidence-grounded agent skills for understanding and reviewing codebases.
Both skills are manual-only: invoke them explicitly with their slash commands.
They analyze source without authorizing code changes, commits, or deployments.

| Skill | Purpose |
| --- | --- |
| [`tdv-code-architecture`](tdv-code-architecture/SKILL.md) | Explain purpose, components, contracts, invariants, end-to-end flows, lifecycle, and failure behavior, with source citations and a newcomer reading path. |
| [`tdv-code-audit`](tdv-code-audit/SKILL.md) | Produce a read-only repository audit with severity-rated findings, strengths, an improvement strategy, and a prioritized task plan. |

The architecture skill includes specialized reference guides and synthetic
[behavioral evaluation fixtures](tdv-code-architecture/evals/README.md).

## Installation

Use the
[`skills` CLI](https://github.com/vercel-labs/skills) to install into a supported
coding agent.

```sh
npx skills add alinsavix/tdvskills
```

The installer prompts you to select skills and target agents. Installation is
project-local by default; add `--global` to make the skills available across
projects:

```sh
npx skills add alinsavix/tdvskills --global
```

To install just one skill:

```sh
npx skills add alinsavix/tdvskills --skill tdv-code-architecture
```

You can also install from a local checkout. Run this from the checkout root:

```sh
npx skills add .
```

## Usage

In your agent, explicitly invoke:

```text
/tdv-code-architecture
/tdv-code-audit
```

Include the target repository or subsystem and any desired scope or output
location. Skill discovery and slash-command support depend on your agent; reload
skills or restart the agent if needed.

Generated guides and audit reports may contain information from the analyzed
repository. Review them separately before sharing publicly.

## License

[MIT](LICENSE).
