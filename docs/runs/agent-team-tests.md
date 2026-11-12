# Agent team tests

Smoke tests of the agent team in scratch folders. Full notes: [agent-team-smoke-tests.md](../agent-team-smoke-tests.md).

**Repo:** No project code.

Figures are measured `subagent_tokens` unless a line says otherwise. The Lead (main chat) is not counted unless stated. Nothing here is estimated; missing figures say "not measured".

## Runs

### Run 8: parse_duration smoke test (2026-10-28)

Lead (sonnet). Scratch folder, no real project. Measured subagent tokens, models from the agent files (worker haiku, reviewer opus). Per-agent rows are in the Cost section of this file under "Agent team smoke test 8".

| Unit | Tokens | Review rounds | Result |
|---|---|---|---|
| `parse_duration()` plus tests | 41,239 (worker 24,130; reviewer 17,109) | 1 | APPROVED round 1, 17 tests |

Lead tokens and total wall time: not measured. Full notes are in [agent-team-smoke-tests.md](../agent-team-smoke-tests.md) (test 8).

Lessons:
- **The ask-first rule works as written.** Two edge-case questions were asked before any worker started, and the small calls were listed as assumptions.
- **The rework path is still untested.** Nothing was rejected, so the findings question, the 3-rejection cap and the 5-round cap have not run yet.

## Cost

### Agent team smoke tests, 2026-10-23: planner / worker / researcher / reviewer

Figures are `subagent_tokens` as reported per agent. Planner rows are the planner agent only; whether they include the workers and reviewers it dispatched is not confirmed. "not reported" means the harness gave no figure and none is guessed here.

| Test | Role | Model | Tokens | Tool uses | Wall time | Result |
| --- | --- | --- | --- | --- | --- | --- |
| 1 clamp() loop | Planner | sonnet | 17,992 | 5 | 56s | approved round 1 |
| 1 clamp() loop | Worker | haiku | 14,180 | n/a | n/a | done |
| 1 clamp() loop | Reviewer | opus | not reported | n/a | n/a | APPROVED |
| 2 researcher read-only | Researcher | haiku | 9,724 | 6 | 6s | passed |
| 3 slugify() loop | Planner | sonnet | 19,506 | 4 | 58s | approved round 1 |
| 3 slugify() loop | Worker | haiku | 15,477 | n/a | n/a | done |
| 3 slugify() loop | Reviewer | opus | not reported | n/a | n/a | APPROVED (missed ß data loss, now a blocking rule) |
| 3 slugify() loop | Stray placeholder researcher | haiku | 6,224 | n/a | n/a | wasted |
| 4 forced rejection | Planner | sonnet | 24,329 | 7 | 1m 31s | approved round 2 |
| 4 forced rejection | Worker, round 1 | haiku | 15,470 | n/a | n/a | omitted per_page check (planted) |
| 4 forced rejection | Reviewer, round 1 | opus | 14,057 | n/a | n/a | CHANGES_REQUESTED, caught it |
| 4 forced rejection | Worker, round 2 | haiku | 15,809 | n/a | n/a | fixed |
| 4 forced rejection | Reviewer, round 2 | opus | not reported | n/a | n/a | APPROVED |

Measured total: 152,768+ subagent tokens (lower bound; three reviewer figures are missing). Parent session tokens are not included.

#### Test 5: slugify() re-run after reviewer tightening, 2026-10-23

Hit the 3-rework cap: blocked rounds 1 to 3, approved round 4. Reviewer found new Unicode corner cases each round.

| Role | Model | Tokens | Result |
| --- | --- | --- | --- |
| Planner | sonnet | 39,545 (11 tool uses, 4m 26s) | approved round 4 |
| Worker rounds 1 to 4 | haiku | 14,845 + 17,710 + 19,983 + 18,100 = 70,638 | done |
| Reviewer round 1 | opus | 15,621 | CHANGES_REQUESTED (letter loss) |
| Reviewer round 2 | opus | 17,493 | CHANGES_REQUESTED (Indic marks, case order) |
| Reviewer round 3 | opus | 18,452 | CHANGES_REQUESTED (orphan accents) |
| Reviewer round 4 | opus | not reported | APPROVED |

Measured for this run: 161,749+ (lower bound, round 4 reviewer missing). Running total across all five tests: 314,517+ (lower bound).

#### Test 6: slugify() with ask-first planner and round-aware reviewer, 2026-10-23

Planner stopped before dispatching and asked which scripts to keep. The user chose "transliterate Latin, drop other scripts". A fresh planner then ran the loop. Approved round 2, 1 rework.

| Role | Model | Tokens | Result |
| --- | --- | --- | --- |
| Planner run 1 (question) | sonnet | 8,745 (1 tool use, 7s) | stopped, returned the script question, dispatched nothing |
| Planner run 2 (loop) | sonnet | 28,011 (6 tool uses, 2m 53s) | approved round 2 |
| Worker round 1 | haiku | 19,189 | done |
| Reviewer round 1 | opus | 19,214 | CHANGES_REQUESTED (3 findings) |
| Worker round 2 | haiku | 23,271 | fixed |
| Reviewer round 2 | opus | not reported | APPROVED |

Measured for this run: 98,430+ (lower bound, round 2 reviewer missing). Running total across all six tests: 412,947+ (lower bound). Models per agent are from the agent files, not confirmed per agent.

### Agent team smoke test 8, 2026-10-28: parse_duration with the Lead setup

Figures are `subagent_tokens` as reported per agent. Lead tokens not measured.

| Task | Role | Model | Tokens | Tool uses | Wall time | Status |
| --- | --- | --- | --- | --- | --- | --- |
| parse_duration, round 1 | Worker | haiku | 24,130 | 8 | 52s | report delivered; Lead verified 17 tests pass |
| parse_duration, round 1 | Reviewer | opus | 17,109 | 5 | 38s | APPROVED (non-blocking: `\d` mutant survives; "0s", "1h0m", trailing newline untested) |

Total: 41,239 measured subagent tokens.
