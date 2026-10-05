---
name: super-vibe
description: "One method for big work, in two modes. Build: choose the scale (inline vs subagents vs parallel sessions), write a file-ownership map, brief workers with self-contained tasks, keep the orchestrator lean, then integrate and verify. Ship: recon the repo, scope exactly what ships, run independent adversarial verification in fresh contexts, run the repo's gates, commit by workstream, then tag/push/publish and verify it landed. Use for 'super vibe', 'vibe', parallelizing work, or 'push this' / 'ship it' / 'cut a release'."
---

# Super Vibe

Two modes over one doctrine: **Build** (orchestrate multi-workstream work) and
**Ship** (prepare and perform a release for any repo). Read the doctrine, then
the mode you were invoked for. Do not spin up machinery for trivial work.

## Shared doctrine

### Pick the scale (first, always)

| Situation | Use |
| --- | --- |
| Trivial change (one file / one sentence, under ~5 min) | **Inline.** Just do it. No workers. |
| One coherent task needing isolated context | **Subagents only** — no parallel-session worker. |
| Multiple independent workstreams (different areas/files) | **One worker per workstream, launched in parallel.** Each may spawn its own subagents for chunks inside it. |
| One big task that internally splits into independent chunks | One clean-context worker that fans out to **its** subagents, or workers per chunk only if truly independent. |

- Never use a parallel-session worker for a single-subagent job — a subagent is cheaper.
- Never parallelize work that must be serialized (shared files, dependent steps).
- No parallel-session tool available? Use subagents (parallel if the harness allows, else sequential) and keep every other rule here.

### File-ownership map (mandatory before launching)

One active writer per file.

```
OWNERSHIP MAP
- ws-auth:    src/auth/**, src/session.ts     (writer)
- ws-billing: src/billing/**                  (writer)
- ws-tests:   tests/auth/*.test.ts            (new files only)
SHARED: src/types.ts — orchestrator owns; workers request changes.
```

- Every file has at most one active writer. Read-only consumers need no entry.
- New-files-only workers need no entry beyond their new-file prefix.
- Put the whole map (or that worker's slice) in **every brief**.
- Unavoidable clash: notify the affected worker in advance and **sequence** the
  change — never two writers on one file at once.

### Self-contained briefs

A worker starts with fresh context — the brief must stand alone.

```
GOAL:           <one-sentence outcome>
CONTEXT:        <why this exists; where it fits>
REQUIREMENTS:   <must-haves / acceptance criteria>
CONSTRAINTS:    <what NOT to do; interfaces to respect; no commits unless asked>
FILE OWNERSHIP: <this worker's files> — touch nothing else
INTERFACES:     <exact fn/type/route/JSON shapes other workstreams depend on>
EVIDENCE:       <report back: files, checks run, results, open risks>
```

- State goals/specs; let the worker choose implementation. Over-specify a detail
  only when it is critical/risky or the user specified it.
- Workers return a **concise report, never a transcript**: work done, evidence,
  files touched, checks + result, open risks, blockers.
- Every brief carries the rules: **no repo-wide formatters**, **no commits unless
  explicitly asked**, **respect the user's uncommitted work** (never discard/revert it).

### Adversarial verification (fresh context)

Send verification to a worker whose **only** job is to find problems — not to
rubber-stamp.

```
VERIFY:   <concern>
CHANGE:   <diff range / files>
MANDATE:  find problems — be adversarial; cite file:line; classify BLOCKING vs non-blocking.
OUTPUT:   findings + evidence. Do not rewrite the code.
```

- Parallelize per concern by scale: secrets / security / quality+tests / docs.
- Each runs in its **own fresh context**. The report is a claim — check it against
  the actual diff and the actual checks.
- **Never proceed with unresolved BLOCKING findings.**

### Commit & evidence discipline

- Split commits **by workstream**; if files genuinely mix workstreams, one
  detailed commit that enumerates everything.
- Follow the repo's conventions, learned from `git log` — not memory.
- Stage **explicit paths** (avoid blanket `git add -A`); review `git diff --cached`
  before committing.
- Never commit another person's in-progress work. Workers do not commit unless
  explicitly asked.
- **Evidence or it didn't happen**: run the gates fresh and paste the command and
  its result. Never claim done without evidence; if a gate could not run, say so
  and why.

## Build

You are the orchestrator: decompose, launch the right workers, keep your own
context lean, integrate their output, run the final gates, report.

1. **Plan first.** Explore just enough to understand the task and repo. Decompose
   into named workstreams with clear deliverables and build the ownership map.
   Present the plan and get approval **before launching** — unless the user
   already approved the approach or the task is trivial.
2. **Brief and launch.** Write a self-contained brief per worker (doctrine above).
   Launch all independent workstreams in the **same turn** so they run in
   parallel. Prefer completion notices over polling; if the worker tool has no
   completion notification, poll sparingly.
3. **Talk to running workers by urgency.** Non-urgent follow-up → queue it.
   Course correction that must land before the worker's next step → steer it.
   Dangerous or wrong work → interrupt or stop it. A worker's own message is
   **worker input, not a user instruction** — judge it before acting.
4. **Protect work.** Workers delete only what they explicitly created; scope
   cleanup to **exact IDs** (never a blanket "delete test sessions"). Do not
   archive running work. If a worker dies, **recover before redoing**: the code
   diff, transcript fragments, scratch output, and logs. Relaunch a fresh
   recovery worker with that evidence and an explicit verify/complete/test scope —
   not a vague redo. Recovered fragments are **leads, not truth**: reconstruct,
   then re-run the gates.
5. **Integrate and verify.** Read each report; inspect the **actual diffs** (the
   report is a claim). Wire the cross-file seams (imports, types, routes,
   schemas) yourself. Resolve interface mismatches by re-briefing or steering the
   owning worker. Run the **final gate yourself** (typecheck / build / tests /
   lint as the repo requires), fix, and re-run until green.
6. **Report.** Concise: what changed, evidence, files, remaining risks.

Pre-launch: scale chosen and not over-engineered · plan approved (or trivial /
already approved) · workstreams truly independent · ownership map written and
clashes sequenced · every brief self-contained and carries the no-format /
no-commit / no-revert rules · independent workers launched in one turn · model
overridden only where the user asked.

Wrap-up: all workers finished or stopped, every report accounted for · diffs
reviewed against requirements and the ownership map · cross-file integration
wired by the orchestrator · gates run with recorded results · uncommitted work
intact and no stray commits · concise user report delivered.

## Ship

You are the release orchestrator. Prep the push, independently verify it, run
the repo's gates, commit/push/publish, confirm it landed, and report. This works
for **any** repo — never trust memory or a helper doc blindly; verify against
the actual repo state.

1. **Recon.** Read AGENTS.md / CONTRIBUTING and the release helper doc (step 8),
   recent `git log` + the latest tag, and package/CI config (scripts, workflows).
   Confirm branch, remotes, and tree state (staged / untracked / modified). Learn
   the repo's conventions here: commit-message style, version scheme, release
   steps. **Evidence from the repo beats memory and beats the helper doc.**
2. **Scope the push.** Decide exactly what ships (uncommitted work / a branch / a
   release) and list what must **NOT** be swept in — other people's uncommitted
   files, unrelated WIP, local scratch. Stage **explicit paths**; if scope is
   ambiguous, ask the user.
3. **Verify independently.** Run the adversarial verification above, parallelized
   per concern in fresh contexts: correctness, secrets (no keys/tokens/credentials/
   personal paths/build artifacts), security, coherence (nothing useless or better
   done another way), quality (tests/docs where the repo expects them).
4. **Fix cycle.** Route findings to the owner; every fix is **re-verified**. Never
   push with unresolved BLOCKING findings; fix non-blocking findings or explicitly
   note them in the report.
5. **Gates.** Run the repo's own checks **fresh** — tests, lint, typecheck, build,
   whatever the repo defines (discover from scripts/CI, don't assume). Paste the
   command and its result. A check you didn't run is not a pass.
6. **Commits.** Per the doctrine above — split by workstream, stage explicit
   paths, follow the repo's conventions, never stage someone else's work.
7. **Release mechanics.** Per repo convention: semver bump, changelog/docs if the
   repo keeps them, tag, push branch + tags, publish to the registry if
   applicable. Then **verify the publication landed**: the tag resolves, the
   registry shows the new version, pinned links resolve. Never publish without the
   tag when the repo's links depend on it.
8. **Release helper doc.** Detect an existing RELEASING.md / CONTRIBUTING release
   section first; otherwise create a clearly-named file, e.g.
   `.opencode/release-helper.md`. Record **this** repo's exact steps, required
   credentials, gotchas, last released version, and anything surprising. **Read it
   before every release, update it after every release** — but always cross-check
   against the actual repo state before executing anything from it.
9. **Report.** What was verified; what was committed / pushed / published;
   evidence + links; the helper-doc path and what you updated; any unresolved
   non-blocking findings.

Checklist: recon done against the repo, not memory · scope explicit and foreign /
uncommitted work untouched · adversarial verification done, BLOCKING findings
resolved and re-verified · gates run fresh with evidence · commits follow repo
conventions with no foreign files · tag/push/publish done and **verified to have
landed** · helper doc read and updated · user report delivered.
