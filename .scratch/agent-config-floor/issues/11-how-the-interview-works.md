# How the interview works, in a new repo and an existing one

Type: prototype
Status: resolved
Blocked by: 10

## Question

Goal 2 is generating the missing artefacts by interviewing the developer. The interview runs in two very different situations, and the difference is not cosmetic.

- **Brownfield** — an existing codebase. The tool reads the code first, then asks each question with a recommended answer already filled in. The developer confirms or corrects.
- **Greenfield** — a new or near-empty repo. Nothing to read. Every answer comes from the developer, who may not know yet.

Build a rough version of both interviews and react to them. Then decide:

- **How many questions is too many?** An interview that gets the artefacts right but takes forty minutes will be abandoned halfway, leaving a repo in a worse state than before it started — half-configured, and the developer now believes the tool does not work.
- **What happens to a half-finished interview?** Resumable, discarded, or does it write what it has? This decides whether abandonment is recoverable.
- **How is a pre-filled recommendation presented?** A confident wrong answer is worse than an open question, because developers accept defaults. Does the tool show its reasoning, its confidence, or the evidence it read?
- **Greenfield honesty.** For a new project, most answers are guesses, so the generated artefacts will be thin and partly wrong. Is that better or worse than nothing? Consider whether greenfield should generate less on purpose, and prompt the team to re-run once there is code to read.
- **Who is being interviewed?** A senior developer who knows the codebase gives different answers from a new joiner. The tool cannot tell them apart, and the new joiner is more likely to be the one running a setup tool.

Link both prototypes as assets. Resolution is the interview shape for each mode, plus the abandonment behaviour.

## Answer

**One interview, capped at six questions, brownfield and greenfield differ only in whether answers arrive pre-filled.**

- **How many is too many:** six is the cap, and it is a cap on *questions asked*, not questions considered. The agent drafts every answer it can from the fixed reading budget and asks only where it genuinely cannot tell. On a well-described brownfield repo the interview may be two questions; on greenfield it is all six. Forty minutes was the failure mode this ticket named, and a hard cap is the only thing that reliably prevents it.
- **What happens to a half-finished interview:** it **writes what it has**, always. Abandonment must never leave the repo worse than before. Anything unanswered is written into the artefact as an explicit `TODO(floor):` line naming the open question, so a half-done run produces honest partial config rather than confident holes. Resuming is re-running the skill; it reads what exists and asks only what is still open.
- **How a pre-filled recommendation is presented: always with its evidence, never bare.** "Test command — I read `pnpm test` from `package.json` scripts. Correct?" A developer accepting a default has then accepted a citation they can check in one glance. Where the agent has no evidence it says so and asks openly rather than guessing. Confidence scores were rejected — a number invites the developer to trust a number instead of the evidence.
- **Greenfield honesty: generate less on purpose.** With no code to read, most answers would be guesses, and a thin honest file beats a rich wrong one — the file will be fed to an agent on every future session, so a wrong claim there costs forever. Greenfield writes the structure with `TODO(floor):` lines where knowledge does not exist yet, and the run ends by telling the developer to re-run once there is code.
- **Who is being interviewed:** the tool assumes the **new joiner**, because that is who runs a setup tool. Every question is answerable from evidence on screen, no question requires history the person may not have, and "I don't know" is an accepted answer that becomes a `TODO(floor):` line rather than a dead end.

The six questions and both flows are specified in [the spec](../spec.md#the-interview). As with [the report](07-what-the-floor-report-looks-like.md), reaction is deferred to the real trial rather than a mock-up.

## Amendment: one interview per artefact, questions driven by discrepancies

Superseded in part by [Where the recommended skills come from](09-where-recommended-skills-come-from.md).

**Withdrawn:** one interview of up to six fixed questions covering everything.

**Now:** the first run produces marked stubs and asks almost nothing. Each artefact is then its own opt-in run with its own interview, and the team works through the stubs one at a time.

**The questioning rule changes, and this is the substantive improvement.** A fixed list asks the same questions of every repo, including the ones whose code already answers them. Instead: **ask only where the code is ambiguous.** The generator reads the part of the repo its artefact concerns, and where it finds the codebase disagreeing with itself — two files following different patterns, two styles of React usage, two ways of structuring an API handler — it surfaces the disagreement and asks which one is the standard. A repo that is internally consistent gets almost no questions, and the questions it does get are the ones that genuinely could not be answered by reading.

**The cap is now six questions per artefact run**, not six for the whole engagement. This is survivable only because the runs are separate and opt-in; thirty questions in one sitting is the abandonment failure this ticket was written to prevent.

**The budget is per run, scoped by the artefact.** A React skill run reads the component directories; a backend skill run reads the API layer. Nothing is unbounded, because the artefact being built is what scopes the read.

**Unchanged and still binding:** evidence with every pre-filled answer and never a confidence score; "I don't know" is accepted and becomes a `TODO(floor):` line; a half-finished run always writes what it has; greenfield generates less on purpose; the assumed reader is the new joiner.
