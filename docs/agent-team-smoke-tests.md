# Agent team log

Log for the Lead / worker / researcher / reviewer team (agents in `~/.claude/agents/`; the Lead is the main chat). One entry per project. Token figures are measured `subagent_tokens` unless marked as an estimate. Raw per-agent numbers also go in `cost.md`.

## 2026-10-23: team smoke tests

Four tests of the new team, run in scratch folders. No real project code was touched.

| Test | Task | Units | Agents (models) | Rework rounds | Subagent tokens | Wall time | Result |
|---|---|---|---|---|---|---|---|
| 1 | `clamp()` plus tests | 1 | planner (sonnet), worker (haiku), reviewer (opus) | 0 | 32,172 measured, reviewer not reported | ~56s | Approved round 1. Reviewer deleted the ValueError check in a copy to prove the test catches it. |
| 2 | Researcher reads the four agent files | 1 | researcher (haiku) | 0 | 9,724 | 6s | Correct citations, nothing modified. |
| 3 | `slugify()` plus tests | 1 | planner, worker, reviewer, plus one stray researcher | 0 | 41,207 measured, reviewer not reported | ~58s | Approved round 1, but the reviewer let through silent letter loss ("Straße" becomes "stra-e") as a non-blocking note. |
| 4 | `paginate()` with a planted omission | 1 | planner, 2 workers, 2 reviewers | 1 | 69,665 measured, final review not reported | ~1m 31s | Reviewer caught the omitted `per_page < 1` check, fresh worker fixed it, approved round 2. |

**Findings**
- Subagent dispatch from the planner works.
- The reject, fix and re-review loop works (test 4).
- The first reviewer was too lenient on data loss. `reviewer.md` now treats silent data loss, missing requirements and untested required behavior as blocking.
- In test 4 the planted worker disclosed the omission despite being told not to. The planner redacted that from the reviewer's input, so the catch was independent.
- The tests did not confirm which model each subagent ran on.

**Totals:** 152,768+ measured subagent tokens across the four tests (lower bound; three reviewer figures are missing). Parent session tokens not included.

## Test 5: slugify() re-run after reviewer tightening

| Task | Units | Agents (models) | Rework rounds | Subagent tokens | Wall time | Result |
|---|---|---|---|---|---|---|
| `slugify()` plus tests, same brief as test 3 | 1 | planner (sonnet), 4 workers (haiku), 4 reviewers (opus) | 3 (cap) | 161,749+ measured, round 4 reviewer not reported | ~4m 26s | Blocked on silent letter loss in round 1 (the tightening works). Rounds 2 and 3 chased Unicode corner cases (Indic marks, math-alphabet letters, orphan accents). Approved round 4. |

**Models for test 5.** Per-agent models (planner sonnet, workers haiku, reviewers opus) were taken from the agent files, not confirmed per agent. ccusage confirmed only at session level, for the whole session including the main chat: Sonnet, Haiku and Opus were all used.

**Findings**
- The data-loss rule fires: test 3 approved round 1, this re-run blocked it.
- The reviewer then over-reviewed. A simple spec became an elaborate implementation, and the run cost about 5x test 3. A diminishing-returns limit for later rounds is the proposed fix, not yet applied.
- The planner chose "keep letters from every script" itself because the reviewer left it open. That is a product choice nobody confirmed.

**Running total, all five tests:** 314,517+ measured subagent tokens (lower bound).

## Test 6: slugify() with ask-first planner and round-aware reviewer

| Task | Units | Agents (models) | Rework rounds | Subagent tokens | Wall time | Result |
|---|---|---|---|---|---|---|
| `slugify()` plus tests, same brief as tests 3 and 5, plus the user's script decision | 1 | 2 planner runs (sonnet), 2 workers (haiku), 2 reviewers (opus) | 1 | 98,430+ measured, round 2 reviewer not reported | run 1 about 7s; run 2 about 2m 53s | Planner stopped and asked about scripts before dispatching. After the answer: round 1 CHANGES_REQUESTED, round 2 APPROVED. |

**Models for test 6.** Per-agent models were taken from the agent files, not confirmed per agent.

**Findings**
- The ask-first rule works. The planner dispatched nothing and returned the question with options.
- Round 1 did not block on "Straße", because the brief now said to transliterate it. It blocked on other letter loss: precomposed Ǿ and Ǽ dropped, and national-alphabet Latin letters (ə, ŋ and others) missing, plus tests that only covered mapped letters.
- Round 2 approved with non-blocking notes only (Ƨ, Ɵ and Ƞ still return "", "a你b" gives "a-b"). No chasing of further corner cases.
- Cost: 98,430+ measured vs test 5's 161,749+ measured, about 39% cheaper (both lower bounds), with 1 rework round instead of 3.
- Known gap: Ƨ/ƨ, Ɵ/ɵ and Ƞ/ƞ still return "" in the approved `slug.py`. These are Latin letters the decision said to keep, so this is a defect that was approved, not a note. The new "Every round" reviewer rule would block it.
- The planner chose "ŋ to ng" inside the worker brief. The reviewer allowed "ng" or "n". That is a small product call the planner did not ask about.

**Running total, all six tests:** 412,947+ measured subagent tokens (lower bound).

## Test 8: parse_duration() with the Lead setup and the new Lead.md rules (2026-10-28)

Run in a scratch folder. No real project code was touched. Numbered 8 because Run 5 in `Orchestration_log.md` already counts as test 7 (the first run with the Lead setup).

| Task | Units | Agents (models) | Rework rounds | Subagent tokens | Wall time | Result |
|---|---|---|---|---|---|---|
| `parse_duration("1h30m")` to seconds, plus unittest tests | 1 | Lead (sonnet, main chat), 1 worker (haiku), 1 reviewer (opus) | 0 | 41,239 measured (worker 24,130; reviewer 17,109) | worker about 52s; reviewer about 38s | Approved round 1. |

**Findings**
- The ask-first rule fired. Before dispatching, the Lead asked two questions a worker would otherwise guess: what bad input does, and which units and limits apply. Both answers were the recommended defaults.
- Small calls were stated as assumptions before the worker started: units in order d, h, m, s with no repeats; lowercase and integers only; a bare number is invalid; surrounding whitespace is stripped.
- The Lead re-checked the worker's report against the real files before sending it to the reviewer: it ran the suite (17 pass) and tried every invalid input itself.
- The reviewer ran its own edge cases ("0s", "1h0m", trailing newline, Unicode digits, lookalike units) and its own mutation run. It approved with non-blocking notes: replacing `[0-9]` with `\d` survives the suite, and "0s", "1h0m" and a trailing newline have no tests. The Lead took no action on them.
- Not exercised: `CHANGES_REQUESTED`, the "ask me which findings to fix" step, the 3-rejection cap and the 5-round cap. Nothing was rejected, so those rules are still unproven.
- Lead tokens and total wall time: not measured. The worker's mutation script and a `revmut` copy are left in the scratch folder.

**Running total, tests 1 to 6:** 412,947+ measured subagent tokens (lower bound), unchanged. Test 8 adds 41,239 measured.
