# How the effort is phased, and what each phase stands up alone

Type: grilling
Status: open
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
