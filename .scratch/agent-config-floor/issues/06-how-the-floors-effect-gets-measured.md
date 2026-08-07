# What the LLM judge assesses, and against what rubric

Type: grilling
Status: resolved
Blocked by: 01

> Reshaped when measurement was deferred to the final phase. Previously "How the floor's effect gets measured" — a quantitative before/after study, now handled by [What telemetry can attribute token spend and quality to a repo](02-what-telemetry-attributes-cost-and-quality-to-a-repo.md) much later. This ticket is the **qualitative** judging that every earlier phase relies on, and it must work with no telemetry whatsoever. A judge with no rubric is just a vibe.

## Question

Effectiveness is judged by an LLM rather than measured. Decide what the judge is actually given, what it returns, and what makes its verdict trustworthy enough to act on.

Decide:

- **What the judge reads.** The config files alone? The config plus the repo it describes — so it can catch instructions that are accurate-sounding but wrong about the codebase? Reading the repo is far more useful and far more expensive; pick, and say why.
- **What it returns.** A pass/fail against the floor, a per-item verdict, a prose critique, or a prioritised list of fixes. This has to survive being handed to a team that did not ask for it — see [What the floor report looks like to a team below it](07-what-the-floor-report-looks-like.md).
- **The rubric.** The judge needs criteria, and the honest source is the floor itself. What does "good" mean for each floor item beyond existing — accurate, current, specific to this repo, not contradicting the code? Write the criteria the judge is scored against.
- **Reproducibility.** Two runs on an unchanged repo should not disagree. Decide the tolerance: is the pass/fail deterministic with only the prose varying, or is disagreement acceptable? A judge that flips verdicts cannot support the assisted engagement.
- **Which agent judges.** Claude Code, Copilot, or either. If the tool is delivered as a skill, the judge is whatever the team runs, and verdicts will differ between them.
- **Self-grading.** If the same tool both generates config and judges it, it will mark its own homework. Decide whether that matters here, and if so, what breaks the loop.

Resolution is the judging design: inputs, output shape, rubric, and the stated limits of what a judged verdict is worth.

## Rescoped by the phasing decision

[How the effort is phased, and what each phase stands up alone](08-how-the-effort-is-phased.md) placed the judge in **phase 2**, not phase 1. Three consequences for this ticket:

- **The judge may assume an agent is running.** Phase 2 runs inside Claude Code or Copilot, so the judge costs no extra infrastructure. It must *not* be designed to run in phase 1, which is deliberately deterministic and requires no agent installed.
- **The rubric and the generator's target are the same object.** What the judge assesses is exactly what phase 2's interview must produce. Write the criteria once and use them in both directions; writing them twice guarantees drift. This also sharpens the self-grading bullet above — the loop is now explicit and deliberate rather than accidental, so decide what breaks it.
- **The judge is only responsible for "present, well-formed and useless".** Phase 1 already catches "no file" (presence) and "malformed file" (valid frontmatter), deterministically. The judge starts above that line, which narrows the rubric considerably.

## Answer

**There is no separate judge. There is one agent that finds gaps and helps close them.**

The word "judge" was the mistake this ticket inherited. A judge grades; the destination says help. The thing being designed is a **gap-finder** whose entire output is a list of work to do, and which then does that work with the developer. Renaming is not cosmetic — it decides the output shape, the reading budget, and what "trustworthy" has to mean.

### What it reads: claims first, then a fixed-budget sweep

Two passes, both bounded, both in one session. No handoff document, no multi-session sweep — those were considered and rejected: they turn help into an audit, and a verdict assembled from summarised handoffs cannot be reproducible.

**Pass 1 — claim check.** Every factual claim in the existing config becomes one lookup against the code. "Run `pnpm test`" opens `package.json`. "Code lives in `src/services`" lists that directory. "We use Prisma" greps for the import. Cost scales with how much the config *says*, not with how large the repo is — a 200-line `CLAUDE.md` costs the same in a five-file project and a 5,000-file monorepo.

**Pass 2 — fixed-budget omission sweep.** Claim checking is blind to what the config never mentions, and "the config forgot the thing that matters most" is a real failure. So the agent also reads a **fixed, small, named set**, the same set the stack detection uses (see [How the tool detects the stack](10-how-the-stack-is-detected.md)):

- every dependency manifest and lockfile it can find
- the directory tree to depth 2
- the README
- CI workflow files
- the config files themselves

That is the whole budget. It does not grow with the repo, it does not recurse, and the agent is instructed to stop rather than explore. Anything the agent cannot establish from that set is reported as **"could not determine"**, never guessed.

The honest limit, stated in the output: this finds gaps against what the repo declares about itself. A repo that lies in its own manifests will be judged on the lie.

### What it returns: a gap list, never a grade

Output is an ordered list of gaps. Each gap carries: which of the six capabilities it belongs to, what is missing or wrong, the evidence (file and line, or the lookup that failed), and the concrete next action.

**No score, no pass/fail, no percentage.** A team below the floor that receives a grade receives a judgement, and constraint 1 says this tool is never that. "You have no code-review prompt; here is what one looks like for this repo" is help. "3/6, FAIL" is an audit. The floor is still the yardstick — the gap list is *derived* from it — but the yardstick is not the message.

Ordering is by what fails first, reusing the ranking already settled in [What capabilities make up the floor](01-what-capabilities-make-up-the-floor.md).

### The rubric is one document, and it is also the generator's target

`floor.md` — one file, owned by the toolkit, holding the criteria for each of the six capabilities. It is read in both directions: the gap-finder checks against it, and the generator writes toward it. Written once, so it cannot drift.

Per capability the criteria are the same four, in this order:

1. **Present** — an artefact exists in a path the target agents actually read.
2. **Well-formed** — parses; frontmatter valid where required.
3. **Specific** — describes *this* repo, not any repo. Generic filler counts as absent.
4. **True** — every claim it makes survives the claim check.

Criteria 1 and 2 were previously assigned to a deterministic phase 1. They are no longer — see the amendment below.

### Reproducibility: near-determinism by construction, not by hope

The requirement is that **the gap list is stable**; the prose around it may vary. Three things buy that, and none of them is "the model is good":

- **The checks are lookups, not opinions.** "Does `package.json` contain a `test` script?" has one answer. Most of the rubric reduces to questions of this shape, deliberately.
- **The reading budget is a fixed named set**, so two runs read the same files.
- **The output has a fixed schema**, so there is nowhere for variation to hide except the prose.

Where a criterion genuinely needs judgement — criterion 3, "specific" — disagreement between runs is **accepted and disclosed**. The agent marks such findings as judgement calls. A team is never told that a judgement call is a fact.

**Stated limit, which goes in the report:** this output is advisory. It is not a certification, it does not gate anything, and two runs may word things differently. That limit is acceptable precisely because nothing downstream enforces the result — constraint 1 already removed every gate that would have needed a harder guarantee.

### Which agent runs it, and self-grading

Whichever agent the team already has. Verdicts will differ somewhat between Claude Code and Copilot; acceptable, because the output is a list of suggested work and not a certification.

The same agent both finds gaps and fills them, so it marks its own homework. This is now **deliberate**, not accidental, and one rule breaks the loop where it matters: **after generating, the agent re-runs the claim check against the code, and any claim it cannot verify is written into the file as a question to the developer rather than as an assertion.** The generator is allowed to be confident; it is not allowed to be confidently wrong in a file that will then be fed to an agent forever.

### Amendment this ticket forces on earlier decisions

The premise that phase 1 is a deterministic non-agent check is **withdrawn**, on the user's instruction. Presence and well-formedness are now checked by the same agent as everything else. See the amendments on [How a team runs the floor tool with zero platform involvement](05-how-a-team-reaches-the-floor-unassisted.md) and [How the effort is phased](08-how-the-effort-is-phased.md).

## Amendment: `TODO(floor):` means absent, and the budget is per run

From [Where the recommended skills come from](09-where-recommended-skills-come-from.md).

**Added to the rubric, as a hard rule on criterion 1 (*present*): an artefact carrying a `TODO(floor):` marker counts as absent.**

The toolkit now writes stub files into the repo for every recommended artefact. Ticket 05 established that every file the toolkit leaves behind counts toward the floor — so without this rule the toolkit would write eight stubs and then report the repo as having skills, manufacturing a false pass with its own hands. The marker is what keeps stubs honest.

Consequence for the report: it states **how many stubs remain unfilled**. A repo stalled at eight empty stubs is told so plainly rather than being counted as progress.

**The reading budget is per run, not per repo.** The fixed budget in this ticket still governs the first run — gap-find, stack detect, write stubs. Each subsequent artefact run gets its own budget scoped to the directories that artefact concerns, which is what makes discrepancy detection possible without ever becoming an unbounded scan.
