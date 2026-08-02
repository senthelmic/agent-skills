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

Resolution is the phase list, each with its standalone value statement and its dependency direction.
