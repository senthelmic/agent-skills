# How the effort is phased, and what each phase stands up alone

Type: grilling
Status: resolved
Blocked by: 01

## Question

The destination commits to phases where each is worth shipping without the next, and to measurement arriving last. Decide the phase boundaries.

Already settled and not up for relitigation: **phase one is helping a team get running**, and **measurement/audit is the final phase**. The open question is everything between, and where exactly the seams fall.

Decide:

- **What is the smallest first phase that is genuinely useful?** A team should get value the first time they run it. Name what phase one delivers and what it deliberately omits.
- **How many phases, and what is the seam between each?** A phase boundary should be a point where you could stop permanently and still have shipped something coherent.
- **What does each phase need from the one before?** Any backward dependency is fine; any forward dependency violates the standalone constraint and has to be designed out.
- **The data question, which is the trap.** The final phase measures, and measurement usually wants history. If earlier phases capture nothing, the final phase starts blind on day one; if they capture something, they have taken on cost for a phase that may never be built. Decide deliberately — capture nothing, capture cheaply-and-locally, or accept a blind start — and state the reasoning. Getting this wrong is only discoverable much later.
- **Does the audit feature exist in any form before the final phase?** A team asking "is my setup good?" is a reasonable phase-one question, and the qualitative LLM judge could answer it without any telemetry. Decide whether that counts as audit-arriving-early or is simply part of helping.

- **Where the two goals fall.** Goal 1 (audit and recommend) and goal 2 (generate by interview) are separable, and goal 1 must work without goal 2. The obvious split is one goal per phase. Confirm or reject it — generating without auditing first is incoherent, but auditing without generating is a complete product.
- **Which skill recommendations land in which phase.** Explicitly deferred to this ticket: which stack-specific skills the tool recommends, and in what order they arrive. A phase-one catalogue covering only the most common stacks is defensible; decide which those are, and what the tool says to a team it cannot yet help.
- **Whether the catalogue gates phase one at all.** [Where the recommended skills come from](09-where-recommended-skills-come-from.md) may conclude that supplying skills is a permanent content commitment. If so, phase one can still *identify* that a team needs a React skill without shipping one — decoupling the tooling from the content problem. Decide whether that is a useful product or an annoying one.

Resolution is the phase list, each with its standalone value statement and its dependency direction.

## Answer

**Five phases, ordered by value per unit of risk.**

| Phase | Delivers | Standalone value if everything after it is cancelled |
|---|---|---|
| **1. Audit** | `npx @org/floor` — computed reach, presence and valid frontmatter across all six capabilities, a dated prescriptive report routing to the vendors' generators. Opt-in `--verify` executes build and test commands, reported separately. | A team sees the whole gap and can clear the entire floor by hand, unaided. |
| **2. Generate — instructions** | `--setup`, the ephemeral skill, the interview, `CLAUDE.md` → `.github/copilot-instructions.md`. **The LLM judge lands here.** | Capabilities 1–3 become nearly free — the bulk of the floor by value. |
| **3. Recommend — skills** | Stack detection and naming: "your stack needs a React skill, a TanStack Query skill, a database skill". Authoring help comes from phase 2's interview. | Ships complete with zero catalogue content in existence. |
| **4. Prompts and sub-agents** | The Claude-native → Copilot translator for capabilities 5–6. | The two non-portable capabilities stop being manual duplication. |
| **5. Final — measurement** | Deferred. Reads the patchy history left behind by phase 1's report files. | Everything above already works without it. |

**Why this order.** Phases 1 and 2 are high value and low risk. Phase 3 carries the catalogue's permanent content commitment — the map's largest unscoped risk. Phase 4 carries the lossy-translator risk that could reopen [Which files and paths satisfy the floor in both agents](04-which-files-and-paths-satisfy-the-floor.md). Both risky phases sit behind two that are already worth shipping.

**Rejected: merging phases 3 and 4** into one "everything above instructions" phase. They fail for unrelated reasons — phase 3 fails if we cannot sustain content, phase 4 fails if the formats do not translate — and merging lets one risk sink the other.

### Phase one is audit-only

No interview, no generation, no ephemeral skill. It deliberately omits stack detection, the catalogue, the Copilot translator, and every recommendation that depends on reading the code.

This is not the "audit, not help" failure constraint 7 warns against. The report is not a score — it ends in instructions, and [What capabilities make up the floor](01-what-capabilities-make-up-the-floor.md) already established that the vendors' generators write most of capabilities 1–3 unaided. **Phase one delivers help as directions to tools that already exist**, and costs us no generation machinery.

Accepted risk: "you are missing six things, go do them" is exactly what a struggling team may ignore. That is the adoption paradox arriving one phase early, with no automation to soften it. [How a team runs the floor tool with zero platform involvement](05-how-a-team-reaches-the-floor-unassisted.md) already answered that adoption is not the mechanism's problem to solve.

### The audit covers all six capabilities from phase one

Not just the ones phase two will generate. Checking presence at a known path is nearly free; a report showing a third of the floor teaches a wrong idea of what "done" means, and the team would have to relearn it twice.

**Consequence, and it reprioritises the map:** the Copilot side of capabilities 5–6 cannot be audited until [What Copilot's saved-prompt and custom-agent formats actually are](12-copilot-prompt-and-agent-formats.md) lands. **That research now gates phase one, not phase four.** It is a documentation lookup, so the cost is small and the ordering is correct.

**Requirement this places on the report:** it must clearly separate **"this is missing"** from **"this exists but we cannot yet help you make it"**. Without that split, phases 2–4 read as broken promises — and that is precisely how a forward dependency would sneak in.

### The floor verdict never executes anything

[How a team runs the floor tool with zero platform involvement](05-how-a-team-reaches-the-floor-unassisted.md) requires the audit to give the same answer twice. Execution is machine-dependent — installed dependencies, network, environment — so a repo could pass on one laptop and fail on another for reasons unrelated to the floor.

- **The verdict** checks only that build, test and run commands are *documented*. Deterministic, machine-independent.
- **`--verify`** additionally runs them and prints a separate section: *"verified on this machine, at this time"*. **Never folded into the pass or fail.**
- Opt-in also matters for a second reason: running commands out of a repository is a developer's choice, not something a report tool does by surprise.

The honest position, which the report must state: **the floor can confirm the commands are written down; only a human running them can confirm they are true.**

### The catalogue is not a phase

Phase 3 ships **stack detection and naming** — it reads the repo and reports "your stack needs a React skill and a TanStack Query skill; you have none". That machinery is complete without a single line of catalogue content existing.

Where the content comes from: **phase 2's interview already knows how to help someone author a file.** Pointed at a named gap, it walks the developer through writing *their* skill. So the tool's answer is "here is the gap, and here is help filling it" — which needs no catalogue.

The catalogue becomes an **optional content stream**, not a phase. It upgrades phase 3 from "help me write one" to "here, take this one", and it can start at any time, or never.

**Hard constraint on [Where the recommended skills come from](09-where-recommended-skills-come-from.md): whatever it decides, that decision must not become a prerequisite for shipping phase 3.** This removes the map's largest unscoped risk from the critical path entirely.

### The data question: capture cheaply and locally

Already fixed by the unassisted-path decision: no telemetry, and the final phase cannot backfill history. What remains is whether anything records history at all.

**The report file is the record.** It already exists for another reason, so this costs almost nothing:

- It gets a **stable path** and a **machine-readable header** — date, floor version, verdict per capability.
- **The tool writes it; the team commits it.** We never commit on their behalf.
- Their git history then becomes a dated record of every run, at zero infrastructure cost, owned entirely by them, with nothing leaving the machine.

Two boundaries:

- **History will be patchy.** Some repos will commit the file, some will not. The final phase must be designed for partial coverage rather than assuming it.
- **The report file is a toolkit artefact and never counts toward the floor**, per the rule set in the unassisted-path decision.

This makes the trap survivable both ways: if the final phase never ships we spent nothing, and if it does ship it is not starting from zero.

Rejected: **capture nothing** leaves the final phase blind for no saving, since the file exists anyway. **Capture in the assisted path only** biases towards teams who already asked for help — the wrong half of the population.

### The LLM judge is phase 2

Audit *does* arrive early, but only its mechanical half.

Phase 1 stays deterministic and requires **no agent installed** — the entire reason `npx` was chosen. An LLM judge in phase 1 would break the same-answer-twice property and quietly require Claude Code or Copilot to be present, turning a low-friction front door into an install.

Phase 2 already runs inside an agent, so the judge rides along at no infrastructure cost. And the rubric is one object seen from two sides: what the judge assesses is exactly what the generator must produce. Building them apart means writing the standard twice.

Phase 1 is not toothless — [Which files and paths satisfy the floor in both agents](04-which-files-and-paths-satisfy-the-floor.md) already mandates presence **plus valid frontmatter**, which is deterministic. So phase 1 catches "no file" and "malformed file"; only "present, well-formed and useless" survives to phase 2.

Stated limit for the report: **phase 1 tells you whether the pieces are there; it does not tell you whether they are any good.**

### Dependency direction — every arrow points backwards

| Phase | Depends on | Direction |
|---|---|---|
| 2 | Phase 1's audit | backward |
| 3 | Phase 2's interview (for authoring help) | backward |
| 4 | Phase 2's generation machinery | backward |
| 5 | Phase 1's committed report files, tolerant of gaps | backward |

Nothing earlier needs anything later. The one plausible forward dependency — phase 1 promising help that only arrives in phase 3 — is designed out by the missing / not-yet-helpable split required above.

The Copilot formats research is a **prerequisite for building phase one**, not a phase dependency: it is a fact the implementation needs, not a capability phase one waits on.

### Tickets this decision rescopes

- [What telemetry can attribute token spend and quality to a repo](02-what-telemetry-attributes-cost-and-quality-to-a-repo.md) — unblocked. Its input is committed report files with patchy coverage, not telemetry.
- [What the LLM judge assesses, and against what rubric](06-how-the-floors-effect-gets-measured.md) — lands in phase 2; same rubric the generator writes against.
- [Where the recommended skills come from](09-where-recommended-skills-come-from.md) — must not gate phase 3.
- [What the floor report looks like to a team below it](07-what-the-floor-report-looks-like.md) — must carry the machine-readable header, the missing / not-yet-helpable split, and the two stated limits above.
- [What Copilot's saved-prompt and custom-agent formats actually are](12-copilot-prompt-and-agent-formats.md) — now gates phase one. Highest-priority research on the map.

## Amendment: phase 1 is no longer deterministic, and phases 1 and 2 merge

Superseded on the user's instruction while resolving [What the LLM judge assesses, and against what rubric](06-how-the-floors-effect-gets-measured.md).

**Withdrawn:** "phase one is audit-only, deterministic, and needs no agent installed."

**Now:** the agent does both, and the first shippable thing is a **working prototype of audit plus generate together** — the user's stated priority is trialling it on real repos with no agent config at all, and an audit that only reports gaps gives such a repo nothing to trial.

Revised phases:

| Phase | Content | Stands alone because |
|---|---|---|
| **1 — Bootstrap** | Gap-find and generate, in one skill run. Covers all six capabilities. Claude Code first; Copilot files generated best-effort. | A repo at zero ends the run with working config. This is the whole product for a self-serve team. |
| **2 — Sharpen** | Stack-aware skill recommendations; the Copilot translation done properly; the interview refined from trial feedback. | Phase 1 already works; this makes its output better-targeted. |
| **3 — Measurement** | Unchanged: the deferred final phase, telemetry, quantitative. | Nothing before it depends on it. |

**Unchanged and still binding:** every phase stands alone, no dependency arrow points forward, the catalogue is not a phase and nothing may depend on it, and the report file is the record — stable path, machine-readable header, committed by the team, history patchy by design.

**Cost of the merge, accepted:** the audit half can no longer be trusted to give byte-identical output across runs. Ticket 06 makes the *gap list* stable and discloses the rest. Nothing gates on the output, so nothing breaks.
