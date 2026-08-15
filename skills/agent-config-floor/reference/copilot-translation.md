# Claude-native to Copilot translation

**Best-effort in phase 1, and marked as such in the output.** The conversion is
lossy. The loss is accepted, not solved.

The paths and frontmatter below were verified against GitHub's and VS Code's own
documentation on 2026-08-14. What has **not** been done is the full field-by-field
loss analysis; that is phase 2.

Whenever you write a translated file, put this line at the top of it:

```markdown
<!-- Translated from <source path> by the agent config floor skill, phase 1.
     Best-effort: placeholder and frontmatter differences are lossy. Review it. -->
```

---

## Skills — no translation needed

Copilot reads `.claude/skills/` natively, with the same `SKILL.md` format and
frontmatter. **Do not translate skills and do not duplicate them.** Writing a
copy into `.github/skills/` creates two files that drift apart for no gain.

---

## Instructions

`CLAUDE.md` → `.github/copilot-instructions.md`.

Mostly a copy, with one mandatory change:

**Flatten every `@import`.** `CLAUDE.md` may reference other files with
`@path/to/file.md`. Copilot does not follow them. Inline the referenced content
in full. An unfollowed import is a silent failure — the file looks present and
delivers nothing.

Also: drop anything that is specific to Claude Code's own features and would
confuse a Copilot session, and keep the self-test line (see
[paths.md](paths.md)), changing the token so a developer can tell which file
answered.

---

## Saved prompts

`.claude/commands/<name>.md` → `.github/prompts/<name>.prompt.md`.

| Claude Code | Copilot | Loss |
|---|---|---|
| `$ARGUMENTS` | `${input:name}` | Copilot's inputs are named; Claude's are positional. A prompt taking free-form arguments has no exact equivalent — name the input and say in the body what it expects. |
| `$1`, `$2` … | `${input:first}`, `${input:second}` … | Same. Names must be invented; invent readable ones. |
| Frontmatter `description` | Frontmatter `description` | Carries over. |
| Frontmatter `argument-hint` | no equivalent | Fold the hint into the body text. |
| Frontmatter `allowed-tools` | `tools:` | Names differ between the two agents. Where a tool has no counterpart, drop it and note the drop in the header comment. |
| `!` shell execution | no equivalent | Cannot be translated. Leave a `TODO(floor):` line saying what the command did. |

---

## Sub-agents / custom agents

`.claude/agents/<name>.md` → `.github/agents/<name>.agent.md`.

| Claude Code | Copilot | Loss |
|---|---|---|
| Frontmatter `name` | `name` | Carries over. |
| Frontmatter `description` (also the routing signal) | `description` | Carries over, but routing behaviour differs between the agents. Do not assume the same delegation happens. |
| Frontmatter `tools` | `tools` | Tool names differ. Drop what has no counterpart and note it. |
| Frontmatter `model` | `model` | Carries over; the model names differ, so drop the value rather than translating it. |
| Body prompt | Body prompt | Carries over. |

**`description` is the only required field.** Copilot also accepts `name`,
`argument-hint`, `tools`, `agents`, `model`, `user-invocable`,
`disable-model-invocation`, `target`, `mcp-servers`, `handoffs` and `hooks`.
Two of those — `argument-hint` and `handoffs` — are documented as unsupported on
github.com, so do not rely on them in a generated file.

---

## The rule when you are unsure

**Never invent a Copilot feature to preserve a Claude one.** If something does
not translate, leave a `TODO(floor):` line naming what was lost and what it did.
A translated file that quietly does less than the original is worse than one
that says which part is missing, because the developer will trust it.
