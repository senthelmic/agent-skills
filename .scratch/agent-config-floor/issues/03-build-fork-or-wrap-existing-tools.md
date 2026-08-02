# Build, fork, or wrap the existing audit tools

Type: research
Status: open

## Question

Several tools already occupy part of this space. Establish how much of the floor check is already solved before committing to build anything.

Evaluate at minimum:

- **ClaudeForge** — CLAUDE.md generator and maintenance tool; enforces a line cap by splitting into sub-files, and has drift-audit, link-check and dependency-rescan modes.
- **agent-docs-audit** — audits AGENTS.md / CLAUDE.md and nested guidance for staleness, duplication, and mirror drift.
- **agent-audit** — forensic scanner over agent configs, instruction files and session logs; ships a large bundled rule set.
- Anything comparable surfaced during the search, including GitHub's own tooling for instructions and skills.

For each, report:

- What it checks or generates, and whether it covers **presence** (our floor check) or only **quality** of existing config.
- Whether it understands **both** Claude Code and Copilot, or Claude only. Per the map's standing constraints, the instructions-layer asymmetry is the actual gap — a Claude-only tool misses it entirely.
- Licence, maintenance signal, and whether it is safe to depend on inside an organisation.
- Whether it is usable as a library or only as an interactive skill.

Resolution is a recommendation: **build fresh, fork, or wrap** — with the reasoning, and specifically what our floor needs that none of them provide.

Note for whoever runs this: enterprise guidance is to treat installing a third-party skill with the same rigour as installing software on a production system. Any recommendation to adopt one of these must account for that.
