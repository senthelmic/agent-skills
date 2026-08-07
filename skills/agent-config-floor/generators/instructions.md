# Generator: project instructions

Fills capabilities 1 and 2 — how to build, test and run, and what the project is
and where its code lives. Capability 3 lives in the same file but has its own
generator; see [coding-standards.md](coding-standards.md).

**Writes to** `CLAUDE.md` or `.github/copilot-instructions.md`, depending on the
target. See [../reference/paths.md](../reference/paths.md).

---

## Step 1 — try the vendor first

- Target includes Claude Code → tell the developer to run `/init`, then check
  and complete its output against [../floor.md](../floor.md).
- Target is Copilot on VS Code → tell them to run "Chat: Generate
  Instructions", then check and complete its output.
- Target is Copilot on JetBrains, Visual Studio or Xcode, or no vendor
  generator is available → generate it yourself, below.

Checking the vendor's output is not optional. `/init` writes a good description
of the code and often says nothing about test commands or conventions.

---

## Step 2 — read

The manifests, the README, the CI workflow files, and the top-level directories.
Nothing else.

---

## Step 3 — ask, only where the code is ambiguous

Likely ambiguities, at most six asked:

- Two test scripts and no way to tell which is the one to run
  (`test`, `test:unit`, `test:ci`).
- A monorepo where the root script and the package script differ.
- A README command that the manifest does not define, or defines differently.
- A dev server that needs an environment file or a running database, where the
  requirement is not written anywhere.
- Directories at depth 1 whose purpose cannot be guessed from the name.

Carry the evidence every time:

> Test command — I read `pnpm test` from the `scripts` block of `package.json`,
> but the README says `npm run test:ci`. Which one should an agent run?

---

## Step 4 — write

Keep it short. This file is fed to an agent on every future session, so every
line costs tokens forever.

```markdown
# <project name>

<Two or three sentences: what this project is and who uses it.>

## Build, test and run

- Install: `<command>`
- Test: `<command>`
- Run: `<command>`
- Lint / typecheck: `<command>`

<Any precondition an agent would otherwise miss — a required env file, a
database that must be running, a build step that must come first.>

## Where the code lives

- `<dir>/` — <what is in it>
- `<dir>/` — <what is in it>

<In a monorepo, list the packages with their paths and what each one is.>

## Conventions

<Written by the coding-standards generator. If that stub is still unfilled,
leave this heading out entirely rather than filling it with generic advice.>

---

If asked whether project instructions are loaded, reply with: FLOOR-OK-<repo-slug>
```

Rules:

- **Commands are written literally**, never described. `pnpm test --filter web`,
  not "run the test script for the web package".
- **Never write a command you did not find** in a manifest, a CI workflow or the
  README. If the developer told you and you cannot verify it, that is fine —
  write it, and see step 5.
- **Greenfield**: write the structure with `TODO(floor):` lines and end the run
  by telling the developer to run this again once there is code.

---

## Step 5 — re-check

For every command in the file, do the one lookup that confirms it exists.
For every path in the file, list it.

- Confirmed → leave it.
- Contradicted → fix it and check again.
- Cannot verify → rewrite it as a `TODO(floor):` question:

```markdown
TODO(floor): is `make dev` the right command to start this locally? I could not
find it in a manifest, a CI workflow or the README.
```

---

## Step 6 — the Copilot copy

If the target is `both`, write `.github/copilot-instructions.md` from this file,
following [../reference/copilot-translation.md](../reference/copilot-translation.md).
**Flatten every `@import`** — Copilot does not follow them.
