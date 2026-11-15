# singularity

Local agent-runtime project (TypeScript monorepo). Feature, architecture and eval detail are left out on purpose; the process and cost data stay.

**Repo:** Local only, no remote.

Figures are measured `subagent_tokens` unless a line says otherwise. The Lead (main chat) is not counted unless stated. Nothing here is estimated; missing figures say "not measured".

## Runs

- 2026-11-10 P0 scaffold: 2 review rounds, r1 finding kind: bypass of a check. Approved and committed.
- 2026-11-10 P1 U1: 2 review rounds, r1 finding kind: test gap. Approved and committed on a phase branch.
- 2026-11-10 P1 U2 (worker sonnet, code that parses untrusted input): round 1 CHANGES_REQUESTED (4 findings: input handling x1, spec mismatch x2, test gap x1). Owner chose to fix all four. Fix worker done and Lead-verified (lint, format, typecheck, build, 147 tests pass). Round 2 review SKIPPED at the owner's request (halted for the day); NOT approved, NOT committed.
- 2026-11-10 P1 U3a: review r1 APPROVED, no blocking findings; committed. Worker figures from the earlier session: not measured.

### 2026-11-10 to 2026-11-11 singularity: seven-unit core and first real-model eval (stopped by the owner, in progress)

- Goal: finish a working core in at most 2 days (owner). Plan trimmed from about 34 units to 7, plus an eval harness. Several later phases deferred.
- Done and committed: the units on separate branches, merged into one integration branch, and baseline eval results. Nothing merged to main (no remote).
- Agents/models: workers sonnet (owner: no opus workers, too much usage), reviewers opus (agent file). No haiku, no researcher. Parallelism: three units built at the same time in separate git worktrees (.worktrees/), and the evals built in parallel with another unit.
- Review rounds: 3 units CHANGES_REQUESTED in round 1, 1 APPROVED. Three further units had no reviewer (Lead checks only, by plan).
- Finding kinds: spec question x1, output handling x1, spec mismatch x1, plus 2 hardening items taken from non-blocking notes.
- First real-model eval: run on a small local model, 3 runs per case. Most cases passed and no tool call was malformed. Results are committed in the project.
- What the eval found that unit tests did not: (1) a model-behaviour gap in a large share of runs (replayed through the real loop it was model behaviour, not a parser bug); (2) an unbounded read that overflowed the model's context. Fix worker was started and then stopped by the owner before finishing; partial edit left uncommitted in a worktree.
- Lessons: (1) Mocks hide model behaviour; the real model found two bugs 800+ passing tests missed. Run the real model as soon as the loop works, not at the end. (2) Fresh git worktrees on Windows came out CRLF and failed prettier; a .gitattributes with eol=lf fixed it for every checkout. (3) Parallel worktrees worked, but lockfile, .gitignore and eslint config conflict at merge; resolve by keeping both ignore lines and regenerating the lockfile with a full install. (4) A timing test (limit 500 ms) failed whenever workers ran in parallel; raised to 2000 ms. (5) The reviewer caught a safety-adjacent regression the worker's own mutant checks did not: check the positive case (the safe thing still works) as well as the negative one. (6) The Lead asked the owner for a server path before searching running processes; another local app's server was already running. Check running processes and existing installs before asking or downloading. (7) The owner's quality reference site is the bar, not the concept; confirmed before spending on polish.
- Owner decisions: no opus workers; one spec question settled in favour of the stricter option; a new package for app glue; download of an official release approved (checksum verified, extracted into a gitignored folder); GPU use for evals pre-approved; another local app's background processes stopped on request; polish only after the core works.
- Deviations: (a) outside-project writes: none known. Reviewers' and workers' temp stayed in .scratch (system temp counts before/after equal in 3 reviews). (b) One worker mutant run briefly made a real HTTP request to a public host (a guard removed on purpose; test prompt only, no project data); guard restored and tested. (c) A reviewer put a probe script inside the project for seconds before moving it to .scratch; a worker left a backup file inside the project and removed it. (d) Downloaded 597 MB from the official release page with the owner's yes; this was avoidable (see lesson 6). (e) Stopped another local app's processes at the owner's request; nothing else touched. (f) Started and stopped the model server twice from a gitignored tools folder; both stopped.
- Not done yet: a reliability fix and a second eval run to compare; review of three units by a reviewer; polish pass; merge to main; deferred phases.

## Cost

### singularity (2026-11-10) Phase 0 scaffold
| Unit | Agent | Model | subagent_tokens | Duration | Verdict |
|---|---|---|---|---|---|
| Upstream survey | researcher | haiku | 58,466 | 2m 28s | report delivered |
| P0 scaffold | worker | haiku | 43,003 | 2m 49s | |
| P0 review r1 | reviewer | opus | 27,496 | 1m 52s | CHANGES_REQUESTED (bypass of a check) |
| P0 review r2 | reviewer | opus | 19,371 | 0m 50s | APPROVED |
Round-1 fix made inline by the Lead (tokens not measured). Lead (main chat) tokens: not measured.
| Design research | researcher | haiku | 83,320 | 3m 32s | report delivered |
| P1 U1 | worker | haiku | 54,156 | 3m 26s | report delivered |
| P1 U1 review r1 | reviewer | opus | 37,342 | 1m 18s | CHANGES_REQUESTED (test gap) |
| P1 U1 review r2 | reviewer | opus | 35,851 | 1m 15s | APPROVED |
Round-1 fixes made inline by the Lead (tokens not measured). Lead (main chat) tokens: not measured.

### singularity (2026-11-10) Phase 1 unit 2
| Unit | Agent | Model | subagent_tokens | Duration | Verdict |
|---|---|---|---|---|---|
| P1 U2 | worker | sonnet | 66,623 | 4m 23s | report delivered |
| P1 U2 review r1 | reviewer | opus | 74,657 | 4m 11s | CHANGES_REQUESTED (4 findings) |
| P1 U2 rework r2 | worker | sonnet | 68,855 | 3m 40s | report delivered; review r2 skipped by owner, not approved |
Lead (main chat) tokens: not measured.
| P1 U3a review r1 | reviewer | opus | 48,576 | 2m 45s | APPROVED |
| P1 U3a worker | worker | not measured | not measured | not measured | earlier session |

### singularity (2026-11-10) Phase 1 units 3c, 3d and Phase 2/3 so far (measured subagent_tokens only)
| Unit | Agent | Model | subagent_tokens | Duration | Verdict |
|---|---|---|---|---|---|
| 3c review r1 | reviewer | opus | 46,164 | 3m 52s | CHANGES_REQUESTED (spec question) |
| 3c fix | worker | sonnet | 48,071 | 2m 01s | report delivered; Lead verified, r2 skipped |
| 3c worker (earlier session) | worker | not measured | not measured | not measured | |
| 3d | worker | sonnet | 74,368 | 4m 37s | report delivered |
| 3d review r1 | reviewer | opus | 49,668 | 3m 15s | CHANGES_REQUESTED (output handling) |
| 3d fix | worker | sonnet | 54,060 | 6m 17s | Lead verified, committed |
| Server unit | worker | sonnet | 58,722 | 3m 22s | report delivered |
| Server review r1 | reviewer | opus | 47,605 | 2m 39s | APPROVED (4 non-blocking, 2 applied) |
| UI unit | worker | sonnet | 83,240 | 5m 33s | no reviewer, Lead verified |
| Scene unit | worker | sonnet | 89,641 | 7m 22s | no reviewer, Lead verified |
| App package unit | worker | sonnet | 106,148 | 7m 32s | no reviewer, Lead verified |
| A1 unit | worker | sonnet | 84,253 | 5m 14s | report delivered |
| A1 review r1 | reviewer | opus | 48,752 | 2m 00s | CHANGES_REQUESTED (spec mismatch); Lead fixed inline |
| Eval harness | worker | sonnet | 129,461 | 9m 35s | no reviewer, Lead verified |
| Reliability fix | worker | sonnet | not measured | stopped by the Lead at the owner's request | killed mid-work, nothing committed |
Totals (measured): workers 727,964 (9 runs); reviewers 192,189 (4 runs); all 920,153. Lead (main chat) tokens: not measured. The real-model eval run itself uses no subagent tokens.
