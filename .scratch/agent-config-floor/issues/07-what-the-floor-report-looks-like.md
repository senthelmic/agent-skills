# What the floor report looks like to a team below it

Type: prototype
Status: resolved
Blocked by: 01, 04

## Question

Make a rough, concrete artifact — the actual output a team sees after running the floor check on a repo that is at zero — and react to it.

The reason this is a prototype rather than a discussion: the tone of this output *is* the product's politics. The same findings written two ways read as either an offer of help or a compliance report, and the invitational constraint lives or dies on which one it feels like. That is not settleable in the abstract.

Produce at least two versions to compare:

- One framed as a **checklist / gap report** — what is missing, what to add.
- One framed as **what the agent is currently missing out on** — what Claude Code and Copilot cannot do in this repo today, and why.

Then decide:

- Does it show a **score or a level**? Scores invite comparison between teams, and comparison is how an invitational tool turns into a league table.
- Does it name the **fix**, or perform it? A report that ends in a diff is more useful and more presumptuous.
- What does it say to a repo that is **already above the floor** — nothing, congratulations, or a pointer to house style worth sharing?
- How does it read if a team lead sees it **without having asked for it**?

Link the prototype as an asset from this ticket. Resolution is the chosen framing and the reasoning.

## Requirements added by later decisions

The report is no longer only a framing question — two resolved tickets have put hard requirements on it. The prototype must satisfy all of these, and the framing must survive them.

From [How a team runs the floor tool with zero platform involvement](05-how-a-team-reaches-the-floor-unassisted.md):

- **Written to a file**, not only printed. That file is the voluntary bridge into the assisted path — a team shares it when it wants help.
- **Dated, and stamped with the floor version it was judged against.** Without this a team cannot tell a stale pass from a current one.
- **Useful on a zero-config repo.** First run, nothing present, still worth reading.

From [How the effort is phased, and what each phase stands up alone](08-how-the-effort-is-phased.md):

- **A machine-readable header** — date, floor version, verdict per capability — at a stable path. Committing it is the team's choice, and their git history becomes the floor's only record. The final phase reads this, so the header's shape is a long-lived commitment.
- **The missing / not-yet-helpable split.** The report must clearly distinguish **"this is missing"** from **"this exists but we cannot yet help you make it"**. Phase 1 audits all six capabilities while only phases 2–4 automate them; without the split, every later phase reads as a broken promise.
- **Two limits stated plainly in the report itself**, not buried: the floor confirms build and test commands are *written down*, not that they work (only opt-in `--verify` touches that, and it is reported separately); and phase 1 says whether the pieces are there, not whether they are any good.
- **The report file never counts toward the floor.** It is a toolkit artefact.

Note the tension worth prototyping directly: a machine-readable header and a "we are here to help" tone pull in opposite directions. If the header makes the artefact feel like a compliance record, that is a finding, not a detail.

## Answer

**Framing chosen: "what your agents cannot do here yet."** Not a checklist, not a gap report, not a score.

The two candidate framings were tested against the one reader who decides this — a team lead who sees the file without having asked for it. A checklist read by that person is a list of things their team failed to do. The same facts written as agent capability read as a list of things the team has not been given yet. Identical content, opposite politics, and constraint 1 makes the second one the only compatible choice.

Concrete consequences:

- **No score, no level, no percentage, no "3/6".** Settled in [What the LLM judge assesses](06-how-the-floors-effect-gets-measured.md) and reaffirmed here for the same reason: a score invites comparison between teams, and comparison turns an invitation into a league table.
- **Every gap is written as a capability sentence**, not a missing-file sentence. "Claude Code cannot run your tests without asking, because no build command is written down" — not "MISSING: build command".
- **It names the fix and offers to perform it.** The run does not end at the report; the report is the point where the developer chooses. Presumptuous only if the fix happens unasked, so it does not.
- **A repo already above the floor** gets a short confirmation and nothing else. No congratulations, no upsell, no pointer to house style. The tool has nothing useful to say and says so in three lines.

**The machine-readable header stays, and the tension is real.** The header is fenced YAML at the top of the file — `date`, `floor_version`, `target`, and a per-capability status — followed by the prose report. Keeping them in one file was chosen over splitting into a data file plus a human file: a split guarantees the two drift apart, and the final phase needs the header to sit at a stable path with the team's git history behind it.

The tension noted in this ticket is confirmed, not dissolved: a fenced data block does make the artefact feel more like a record and less like an offer. Mitigation is placement and wording — the header is compact, the prose immediately below it opens with what the agents cannot do, and the header uses `status: absent` rather than `status: fail`.

**All requirements from the later decisions are carried into the format:** written to a file at a stable path, dated and version-stamped, useful on a zero-config repo, the missing / not-yet-helpable split, both limits stated in the report body rather than buried, and the report file never counts toward the floor.

The literal format is specified in [the spec](../spec.md#the-report). Reaction is deferred to the real trial the user is about to run on repos with no agent config — that is a better prototype than a mock-up, and it is available now.
