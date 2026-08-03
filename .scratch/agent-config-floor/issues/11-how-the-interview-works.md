# How the interview works, in a new repo and an existing one

Type: prototype
Status: open
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
