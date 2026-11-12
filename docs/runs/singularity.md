# singularity

Agent runtime with a shell gate, server, UI and a real-model eval harness (TypeScript monorepo).

**Repo:** Local only, no remote.

Figures are measured `subagent_tokens` unless a line says otherwise. The Lead (main chat) is not counted unless stated. Nothing here is estimated; missing figures say "not measured".

## Runs

- 2026-11-10 singularity P0 scaffold: pnpm TS monorepo (6 packages), 2 review rounds, r1 finding kind: bypass of a check (release-age exclude). Approved; committed c9d1cd3. Full entry after project.
- 2026-11-10 singularity P1 U1 events package + bus: 2 review rounds, r1 finding kind: test gap. Approved; committed a48a206 on branch phase-1/events. Full entry after project.
- 2026-11-10 singularity P1 U2 LLM client + agent loop (branch phase-1/runtime-loop, from phase-1/events): worker sonnet (parser of untrusted input). Round 1 CHANGES_REQUESTED (4 findings: input handling x1 bad usage numbers crash the loop, spec mismatch x1 SSE multi-line data, spec mismatch x1 open message streams not closed on error/abort, test gap x1). Owner chose all four plus globals.d.ts instead of DOM lib, tests left untyped. Fix worker done and Lead-verified (lint, format, typecheck, build, 147 tests pass). Round 2 review SKIPPED at the owner's request (halted for the day); NOT approved, NOT committed. Full entry after project.
- 2026-11-10 singularity P1 U3a file tools (branch phase-1/shell-tools): review r1 APPROVED, no blocking findings; committed cae8788. Non-blocking: odd Windows names, atomic writes (deferred to T3), subagent auto-deny (3b/A1). Worker figures from the earlier session: not measured. Full entry after project.

### 2026-11-10 to 2026-11-11 singularity: shell gate, server/UI/scene/app/subagents, first real-model eval (stopped by the owner, in progress)
- Goal: finish a working core in at most 2 days (owner). Plan trimmed from about 34 units to 7: classifier (3c), shell tool + broker (3d), server, UI, scene, app glue, subagents; plus an eval harness. Deferred: sessions/memory/skills, code exec/browser, galaxy polish, Electron, smart-mode judge, benchmark repair.
- Done and committed: 3c classifier 3124cf5, 3d run_shell + broker a936812, protocol c3e25ac, server 44bbac6, UI 6c2d350, scene cfedab4, integration branch phase-2/integration (merge of all, later ff to app 9896ece, subagents c4aa42d, evals merge 610cf9e), baseline eval results 841346c. Nothing merged to main (no remote).
- Agents/models: workers sonnet (owner: no opus workers, too much usage), reviewers opus (agent file). No haiku, no researcher. Parallelism: server, UI and scene built at the same time in separate git worktrees (.worktrees/), evals built in parallel with subagents.
- Review rounds: 3c r1 CHANGES_REQUESTED (spec question: Windows format of the system drive, owner chose hardline for C:/disk 0); 3d r1 CHANGES_REQUESTED (PowerShell errors reached the model as CLIXML); server r1 APPROVED; A1 r1 CHANGES_REQUESTED (children could run no shell command at all, even echo). UI, scene, app and evals had no reviewer (Lead checks only, by plan).
- Finding kinds: spec question x1, output handling x1, spec mismatch x1 (A1 child shell denial), plus 2 hardening items taken from non-blocking notes.
- First real-model eval (Qwen3.5-9B Q4_K_M, llama.cpp b12259, 16 cases x 3 runs, temp 0.2): 39 of 48 passed (81%), 0 of 71 tool calls malformed, median 53.6 tokens/s, median 2.8 s per run. Safety: hardline 3/3, injection 3/3, outside-workspace 3/3, danger 2/3 (model took no action once). Results are in packages/app/evals/results/ (baseline commit 841346c).
- What the eval found that unit tests did not: (1) in 18 of 48 runs the last model turn had reasoning but no answer text (replayed through the real loop: 1 of 8, so model behaviour, not a parser bug); (2) read_file can return 256 KB into an 8192-token context, which gave HTTP 400. Fix worker was started and then stopped by the owner before finishing; partial edit left uncommitted in .worktrees/reliability.
- Lessons: (1) Mocks hide model behaviour; the real model found two bugs 800+ passing tests missed. Run the real model as soon as the loop works, not at the end. (2) Fresh git worktrees on Windows came out CRLF and failed prettier; a .gitattributes with eol=lf fixed it for every checkout. (3) Parallel worktrees worked, but lockfile, .gitignore and eslint config conflict at merge; resolve by keeping both ignore lines and regenerating the lockfile with a full install. (4) A timing test (limit 500 ms) failed whenever workers ran in parallel; raised to 2000 ms. (5) The reviewer caught a safety-adjacent regression the worker's own mutant checks did not (child shell fully denied): check the positive case (safe thing still works) as well as the negative one. (6) The Lead asked the owner for a llama-server path before searching running processes; another local app's llama-server was already running. Check running processes and existing installs before asking or downloading. (7) The owner's quality reference site is the bar, not the concept; confirmed before spending on polish.
- Owner decisions: no opus workers; hardline for formatting C:/disk 0; new packages/app; download of the official llama.cpp release approved (sha256 checked, extracted to .tools/llama.cpp/b12259, gitignored); GPU use for evals pre-approved; another local app's background processes stopped on request; scene polish only after the core works.
- Deviations: (a) outside-project writes: none known. Reviewers' and workers' temp stayed in .scratch (system temp counts before/after equal in 3 reviews). (b) One worker mutant run briefly made a real HTTP request to example.com (loopback guard removed on purpose; test prompt only, no project data); guard restored and tested. (c) A reviewer put a probe script at packages/server/.probe.mjs for seconds before moving it to .scratch (inside the project); a worker left a .scratch_bak file inside the project and removed it. (d) Downloaded 597 MB from github.com/ggml-org/llama.cpp with the owner's yes; this was avoidable (see lesson 6). (e) Stopped another local app's python, two uv and llama-server processes at the owner's request; nothing else touched. (f) Started and stopped llama-server twice from .tools; both stopped.
- Not done yet: reliability fix (empty-turn retry, context-aware read cap, empty-final flag in the eval report) and a second eval run to compare; review of UI/scene/app by a reviewer; polish pass; merge to main; deferred phases listed above.

## Cost

### singularity (2026-11-10) Phase 0 scaffold
| Unit | Agent | Model | subagent_tokens | Duration | Verdict |
|---|---|---|---|---|---|
| Upstream survey | researcher | haiku | 58,466 | 2m 28s | report delivered |
| P0 scaffold | worker | haiku | 43,003 | 2m 49s | |
| P0 review r1 | reviewer | opus | 27,496 | 1m 52s | CHANGES_REQUESTED (supply-chain bypass) |
| P0 review r2 | reviewer | opus | 19,371 | 0m 50s | APPROVED |
Round-1 fix made inline by the Lead (tokens not measured). Lead (main chat) tokens: not measured.
| Hermes approval research (singularity) | researcher | haiku | 83,320 | 3m 32s | report delivered |
| P1 U1 events | worker | haiku | 54,156 | 3m 26s | report delivered |
| P1 U1 review r1 | reviewer | opus | 37,342 | 1m 18s | CHANGES_REQUESTED (test gap: EventOf narrowing untested) |
| P1 U1 review r2 | reviewer | opus | 35,851 | 1m 15s | APPROVED |
Round-1 fixes made inline by the Lead (tokens not measured). Lead (main chat) tokens: not measured.

### singularity (2026-11-10) Phase 1 unit 2
| Unit | Agent | Model | subagent_tokens | Duration | Verdict |
|---|---|---|---|---|---|
| P1 U2 client + loop | worker | sonnet | 66,623 | 4m 23s | report delivered |
| P1 U2 review r1 | reviewer | opus | 74,657 | 4m 11s | CHANGES_REQUESTED (4 findings) |
| P1 U2 rework r2 | worker | sonnet | 68,855 | 3m 40s | report delivered; review r2 skipped by owner, not approved |
Lead (main chat) tokens: not measured.
| P1 U3a review r1 | reviewer | opus | 48,576 | 2m 45s | APPROVED |
| P1 U3a worker | worker | not measured | not measured | not measured | earlier session |

### singularity (2026-11-10) Phase 1 units 3c, 3d and Phase 2/3 so far (measured subagent_tokens only)
| Unit | Agent | Model | subagent_tokens | Duration | Verdict |
|---|---|---|---|---|---|
| 3c classifier review r1 | reviewer | opus | 46,164 | 3m 52s | CHANGES_REQUESTED (spec question: Windows format) |
| 3c fix (format C: hardline) | worker | sonnet | 48,071 | 2m 01s | report delivered; Lead verified, r2 skipped |
| 3c classifier worker (earlier session) | worker | not measured | not measured | not measured | |
| 3d shell tool + broker | worker | sonnet | 74,368 | 4m 37s | report delivered |
| 3d review r1 | reviewer | opus | 49,668 | 3m 15s | CHANGES_REQUESTED (CLIXML output) |
| 3d fix (CLIXML + 2 hardening) | worker | sonnet | 54,060 | 6m 17s | Lead verified, committed a936812 |
| S0/S1 server | worker | sonnet | 58,722 | 3m 22s | report delivered |
| S0/S1 review r1 | reviewer | opus | 47,605 | 2m 39s | APPROVED (4 non-blocking, 2 applied) |
| U1/U2 chat UI | worker | sonnet | 83,240 | 5m 33s | no reviewer, Lead verified |
| V1/V2 galaxy scene | worker | sonnet | 89,641 | 7m 22s | no reviewer, Lead verified |
| App package + scene dock | worker | sonnet | 106,148 | 7m 32s | no reviewer, Lead verified |
| A1 subagents | worker | sonnet | 84,253 | 5m 14s | report delivered |
| A1 review r1 | reviewer | opus | 48,752 | 2m 00s | CHANGES_REQUESTED (children could run no shell command); Lead fixed inline |
| Eval harness | worker | sonnet | 129,461 | 9m 35s | no reviewer, Lead verified |
| Reliability fix (empty final turn, read cap) | worker | sonnet | not measured | stopped by the Lead at the owner's request | killed mid-work, nothing committed |
Totals (measured): workers 727,964 (9 runs); reviewers 192,189 (4 runs); all 920,153. Lead (main chat) tokens: not measured. The real-model eval run itself (llama-server) uses no subagent tokens.
