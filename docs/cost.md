# Cost log

Subagent token usage as reported by Claude Code (`subagent_tokens`). Rows marked pending are still running. The parent session's own tokens are not included because the session does not report them per task.

## FocusPet, 2026-10-22: tools, robot sprite, robot voice

| Task | Issue | Role | Model | Tokens | Tool uses | Wall time | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Calculator, weather, eval | #7 | Owner A | sonnet | 287,547 | 102 | 28m 3s | done |
| Robot sprite, drop three.js | #8 | Owner B | sonnet | 267,154 | 97 | 13m 34s | done |
| Robotic male voice | #9 | Owner C | sonnet | 129,687 | 21 | 2m 24s | done |
| Voice diff review | #9 | Reviewer | sonnet | 76,884 | 4 | 28s | done |

Finished so far: 761,272 subagent tokens.

## Notes

- All subagents run on sonnet per `pstack-models.md`, in the lean setup (no swarm lanes).
- Update this table as the pending rows report.

## FocusPet, 2026-10-22 (second batch): issue fixes

| Task | Issue | Role | Model | Tokens | Tool uses | Wall time | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| CI split: fast PR, full main | #18 | Worker | haiku | 119,793 | 29 | 4m 30s | done, in review |
| Rain in inches | #13 | Worker | haiku | 95,658 | 31 | 4m 26s | done, in review |
| private_interfaces warnings | #14 | Worker | haiku | 78,028 | 17 | 4m 16s | done, in review |
| Debug-only tool log | #11 | Worker | haiku | 95,566 | 27 | 5m 52s | done, in review |
| Review: private_interfaces | #14 | Reviewer | opus | 71,757 | 4 | 30s | done, ship |
| Review: CI split | #18 | Reviewer | opus | 79,292 | 6 | 1m 4s | done, fix first (cache), fixed |
| Review: rain in inches | #13 | Reviewer | opus | 79,616 | 9 | 53s | done, fix first (precision), fixed |
| Review: debug tool log | #11 | Reviewer | opus | 74,920 | 5 | 40s | done, ship |

Finished in this batch: 694,630 subagent tokens (4 haiku workers 389,045 + 4 opus reviewers 305,585). The parent session is not counted in the rows above.

### Advisor passes before opening PRs (opus, read-only)

| Pass | Target | Tokens | Tool uses | Result |
| --- | --- | --- | --- | --- |
| 1 | PR #23 (#18) | 85,434 | 6 | fix first (cache-key story wrong, dev cache would not save) |
| 1 | #14 branch and body | 71,702 | 4 | fix first (metadata only) |
| 1 | #11 branch and body | 74,821 | 5 | fix first (body wording) |
| 1 | #13 branch and body | 76,544 | 4 | fix first (body wording) |
| 2 | PR #23 | 78,571 | 6 | one sentence, fixed |
| 2 | bodies 14, 11, 13 | 79,117 | 6 | #13 wording, fixed |

Advisor total: 466,189 tokens. PRs opened afterwards: #23 (earlier), #24 (#14), #25 (#11), #26 (#13).

### Session totals from `ccusage session`, read at the end of 2026-10-22

| Session | Last activity | Models | Output tokens | Cost |
| --- | --- | --- | --- | --- |
| First (fixes #19, workers) | 10:15Z | opus-5-5 $1.92, haiku-5-5 $0.24 | 66,046 | $2.16 (8,468,688 tokens) |
| Second (reviews, PRs #23-#26, issues #27-#30) | 11:32Z | sonnet-5-5 $4.70, opus-5-5 $2.82 | 125,887 | $7.51 (29,814,107 tokens) |

- The second session shows a sonnet row, which is the parent session. The first session showed none, so its $2.16 was the subagents only and the parent's turns in it are missing.
- Combined for the two sessions: about $9.67 and 38.3M tokens, mostly cache reads. Treat it as a floor for the first session.
- Only these two rows were copied; no paths or other sessions.

### Day total from ccusage, 2026-10-22 (all projects, not FocusPet alone)

| Day | Models | Input | Output | Cache create | Cache read | Total tokens | Cost |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 2026-10-22 | haiku-5-5, opus-5-5, sonnet-5-5 | 1,180 | 418,590 | 1,937,338 | 115,888,146 | 118,245,254 | $20.55 |

- This covers every project and session that day, including the parent session and the subagents above. It cannot be split into a FocusPet or parent-session figure. For that, run `ccusage session` and copy only the totals for this session.
- Cache reads are 98% of the tokens, so the token total says little; the cost is the usable number.
### This session, from `ccusage session` (read 2026-10-22; last activity 10:15Z, so later turns may be missing)

| Model | Input | Output | Cache create | Cache read | Cost |
| --- | --- | --- | --- | --- | --- |
| opus-5-5 | 44 | 14,063 | 275,406 | 1,293,463 | $1.92 |
| haiku-5-5 | 158 | 51,983 | 384,633 | 6,448,938 | $0.24 |
| Total | 202 | 66,046 | 660,039 | 7,742,401 | $2.16 (8,468,688 tokens) |

- ccusage lists only opus and haiku for this session. The parent ran as sonnet-5-5 and no sonnet row appears, so this is probably the subagent transcripts alone and the parent's own cost may be missing. Treat $2.16 as a floor, not the full session cost.
- Only this session's row was copied; the 69 other sessions in the output were left out.

- ccusage warned that pricing is missing for `glm-5.3-flash:cloud` and `glm-5.3:cloud`, so the cost excludes those models.

## FocusPet, 2026-10-22 (third batch): #30 + #27 PR 1, #27 PR 2, #28, #29

Parent session is sonnet-5-5; workers are haiku @medium, advisors opus. Subagent tokens only; the parent is not counted.

| Task | Issue | Role | Model | Tokens | Tool uses | Wall time | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Eval fixture + dry-day baseline | #30, #27 PR 1 | Worker | haiku | 157,133 | 56 | 5m 6s (resumed once) | done, de18a15, in advisor review |
| she-her strings | #29 | Worker | haiku | 178,507 | 64 | 6m 47s | done, 1abd47f, in advisor review |
| Review: eval PR (pass 1) | #30, #27 PR 1 | Advisor | opus | 95,169 | 9 | 1m 39s | done, fix first (routing prompt, fidelity holes, meta) |
| Review: she-her PR (pass 1) | #29 | Advisor | opus | 112,622 | 10 | 1m 37s | done, fix first (two "he" strings, scope wording) |
| Fix round: she-her PR | #29 | Worker | haiku | 107,700 | 17 | 1m 39s | done, 8caf7b9, pushed |
| Fix round: eval PR | #30, #27 PR 1 | Worker | haiku | 188,777 | 52 | 7m 34s | done, 9a5c2d8 + 88faf49 |
| Review: eval PR (pass 2) | #30, #27 PR 1 | Advisor | opus | 114,089 | 22 | 3m 11s | done, ship after F1-F3 |
| Final small fixes: eval PR | #30, #27 PR 1 | Worker | haiku | 125,862 | 30 | 3m 57s | done, baff640, pushed (see incident) |
| Model card in use | #28 | Worker | haiku | 108,429 | 31 | 3m 58s | done, f6c0acd, in advisor review |
| Review: Model card PR (pass 1) | #28 | Advisor | opus | 83,935 | 8 | 54s | done, ship after 3 small fixes |
| Fix round: Model card PR | #28 | Worker | haiku | 89,054 | 13 | 47s | done, 5cd4c67, pushed |
| Investigate: sprite does not rescale on resize | n/a | Worker | haiku | not reported | | | stopped by me (headless Edge relaunches, error sound); no diagnosis |
| Live window check (resize and scroll) | issue C | Worker | haiku | not reported | | | stopped; measured by me over the debug port instead |
| Rain routing + measurement | #27 PR 2 | Worker | haiku | 144,404 | 56 | 3m 38s | done, bde10b1 + 6189aa0, in advisor review |
| Review: rain routing PR (pass 1) | #27 PR 2 | Advisor | opus | 93,374 | 11 | 1m 24s | done, fix first (regex too broad, NOTES overclaims) |
| Fix round: rain routing | #27 PR 2 | Worker | haiku | 131,803 | 28 | 4m 10s | done, ec1af9a + 42cff0e, pushed |
| Panel scroll fix | #36 | Worker | haiku | 113,985 | 26 | 3m 21s | done, 861d3bd, in advisor review |
| Review: panel scroll PR (pass 1) | #36 | Advisor | opus | 86,521 | 9 | 1m 8s | done, fix first (rail has same bug) |
| Measure rail footer on fix build | #36 | Worker | haiku (low effort) | 97,163 | 10 | 2m 23s | done; confirmed rail 38px taller than viewport |
| Fix rail + tighten gate | #36 | Worker | haiku (medium) | 120,822 | 25 | 3m 24s | done, 715842e, pushed |

## FocusPet, 2026-10-22 (fourth batch): #38

Parent session is sonnet-5-5; all code written inline by the parent (small change), one opus advisor. Subagent tokens only; the parent is not counted.

| Task | Issue | Role | Model | Tokens | Tool uses | Wall time | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Review: rain follow-up PR (pass 1) | #38 | Advisor | opus (medium) | 80,589 | 10 | 36s | done, fix first (double assistant turns, answer not persisted, clear on failure, regex gaps); fixed inline by the parent |
| Review: rain follow-up PR (pass 2, asked by owner) | #38 | Advisor | opus (medium) | 84,300 | 8 | 47s | done, fix first (user row persisted at start); fixed inline; stale-cancel adoption hole left, predates PR |

- Not delegated on purpose: the change is 3 files, about 25 lines, so a worker's cold start would cost more than writing it. Floor figure: the parent's own tokens are not included.
- Advisor total for #38: 164,889 tokens over two passes (the second was an owner request, not the one-pass rule).
- Hand test round 1 failed (answer dropped on silent cancel); fixed inline in a fourth commit, retest passed. No third advisor pass (small change; owner asked for two).

## FocusPet, 2026-10-22 (fifth batch): #34 PR 1 (mood-leak measurement)

Parent session is sonnet-5-5; script and notes written inline by the parent (about 180 lines plus notes, narrow scope), advisors opus. Subagent tokens only; the parent is not counted, so figures are a floor.

| Task | Issue | Role | Model | Tokens | Tool uses | Wall time | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Review: mood-leak PR (pass 1) | #34 | Advisor | opus (medium) | 79,183 | 6 | 38s | done, fix first (temperature 0.2 vs app 0.7, unsanitised scoring, NAMED false positive); fixed inline, baseline re-run |
| Review: mood-leak PR (pass 2) | #34 | Advisor | opus (medium) | 82,013 | 9 | 1m 2s | done, fix first (eval posted /api/chat but the app uses /api/generate; scorer gaps); fixed inline, baseline re-run twice (53/70 both) |

- Local Ollama runs for the baselines (gemma3:4b, 42 and 70 trials) are not billed tokens.
- A scripted edit chain failed once and committed a file deletion under a misleading message before I reset it (local, never pushed). Lesson: chain edits with `&&`, not `;`.
- Advisor total for #34 PR 1: 161,196 tokens over two passes (eval PR, so two per the rule).

## FocusPet, 2026-10-22 (sixth batch): ci-cheaper

Parent session is sonnet-5-5; workflow edits written inline by the parent (one file plus stale-text fixes), advisors opus. Subagent tokens only; the parent is not counted, so figures are a floor.

| Task | Issue | Role | Model | Tokens | Tool uses | Wall time | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Review: ci-cheaper PR (pass 1) | ci-cheaper | Advisor | opus (medium) | 81,145 | 6 | 56s | done, fix first (stale README, tools/README, ci.yml comment, ooxml hint; wrong 18 min figure; no release compile in CI); fixed inline, added `cargo check --release` |
| Review: ci-cheaper PR (pass 2) | ci-cheaper | Advisor | opus (medium) | 77,685 | 5 | 38s | done, fix first (shared cache key would leave release-check artifacts unsaved; run-time figures imprecise); fixed inline with a separate job |

- Pushed #38 (`rain-followup-38`) and #34 PR 1 (`mood-digits-34`) earlier in the day; neither is merged at the time of writing.
- Advisor total for ci-cheaper: 158,830 tokens over two passes.

## FocusPet, 2026-10-22 (seventh batch): #34 PR 2 (mood digits fix)

Parent session is sonnet-5-5; code, tests and eval run inline by the parent (about 25 lines of code plus tests and notes), advisor opus. Subagent tokens only; the parent is not counted, so figures are a floor.

| Task | Issue | Role | Model | Tokens | Tool uses | Wall time | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Review: mood-digits fix PR (pass 1) | #34 | Advisor | opus (medium) | 83,169 | 8 | 1m 15s | done, fix first (regex dropped legitimate sentences and code lines, quadratic on long lines, no false-positive tests); fixed inline, eval re-run |

- Merged by the owner before this batch: #38 (as #41), #34 PR 1 (#42), ci-cheaper (#43).
- Local Ollama runs (the after eval, 70 trials, run twice because the first had a dirty tree) are not billed tokens.
- Process slip: the first after-run recorded dirty=true because an uncommitted CLAUDE.md edit from the ci-cheaper branch was carried over. Committed it and re-ran.
- Advisor total for #34 PR 2: 83,169 tokens (one pass, behaviour PR). Day total of advisor passes logged above: #38 164,889; #34 PR 1 161,196; ci-cheaper 158,830; #34 PR 2 83,169 = 568,084 tokens, a floor (parent sessions not counted).
- HANDOFF.md updated on branch `handoff-update-2` (docs only, skips CI).


## Agent team smoke tests, 2026-10-23: planner / worker / researcher / reviewer

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

### Test 5: slugify() re-run after reviewer tightening, 2026-10-23

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

### Test 6: slugify() with ask-first planner and round-aware reviewer, 2026-10-23

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

## Run 5: FocusPet #34 state-number rule (2026-10-24), first run with the Lead setup (main chat plans)

| Role | Model | Tokens | Result |
| --- | --- | --- | --- |
| Worker: persona rule, backstop, eval | haiku | 125,044 | done |
| Reviewer round 1 | opus | 63,383 | CHANGES_REQUESTED (blocker: false positives) |
| Worker: seeded eval, round-1 code | haiku | 55,766 | measured |
| Worker: rework + note stripper | haiku | 109,687 | done |
| Reviewer round 2 | opus | 60,918 | APPROVED |
| Worker: seeded eval, final code | haiku | 49,715 | measured |

Measured subagent total: 464,513. Lead (main chat) tokens not measured. Wall time not measured. Models are from the agent files.

### Run 5 continued (same day), measured subagent tokens

| Role | Model | Tokens |
| --- | --- | --- |
| Worker: read-time filter + stripper fixes | haiku | 145,092 |
| Reviewer round 3 | opus | 70,756 (APPROVED) |
| Worker: subject rule | haiku | 131,642 |
| Reviewer round 4 | opus | 72,460 (CHANGES_REQUESTED) |
| Worker: read-only, number-adjacent rule | haiku | 165,566 |
| Reviewer round 5 | opus | 78,938 (CHANGES_REQUESTED, third rejection, owner chose narrower rule) |

Subtotal 664,454; with the first part of Run 5 (464,513) 1,128,967. Round 6 worker, its review and doc work not yet counted. Lead tokens not measured.

### Outcome
PR #51, "Part of #34". Final v3.1 scores: clean 94/95, seeded 66/95. The final read filter hides none of the eval's notes, so the seeded gain comes from the persona rule and the reply backstop alone.

### Lessons
- **Approvals weren't final.** Rounds 2 and 3 were APPROVED, but owner and outside review reopened the work both times. The cap counts rejections, not rounds, so the unit ran 6 review rounds and 1.1M+ subagent tokens, about 3x Run 3. Next: also cap total rounds per unit.
- **Fuzzy text rules eat rounds.** Rounds 3 to 5 went to tuning one heuristic: telling Bit's invented numbers apart from the user's real ones. When a rule needs a third rework, stop and pick the smallest safe version.
- **No data loss beat a better score.** Stripping notes on write lifted seeded to 88/95 but destroyed real progress notes ("My draft is 50% done."). The owner chose read-only notes and a narrow read filter instead, accepting seeded at 66/95. The reviewer's data-loss rule decided the design.
- **The eval caught what the unit tests didn't.** Unit checks passed throughout; the seeded scenario showed the memory loop, and hand tests found the leaks the scorer missed.
- **The rejection cap worked.** The third rejection stopped the loop and brought the decision to the owner, instead of a fourth automatic round.

2026-10-24 FocusPet #34 final pass: reviewer (opus) 74,109; worker (haiku) 109,276 subagent_tokens, measured. Total 183,385.
Lessons: (1) a green suite did not catch the backstop deleting factual answers; a reviewer probe of ordinary sentences did, so brief workers with a keep list of ordinary text. (2) Name the review base commit; a stale local main inflated the diff. (3) The worker pass was cheap (109,276) next to the reviewer (74,109) only because the owner capped it at one pass; uncapped rework on this unit ran 1.1M+ earlier. (4) Raw-vs-shown reply checks via the eval harness cost nothing in subagent tokens.
2026-10-24 FocusPet #49 round 1: worker (haiku) 104,517; reviewer (opus) 78,630 subagent_tokens, measured. Round 2 worker pending.
2026-10-24 FocusPet #49 round 2 worker (haiku, stopped early, no commit): 116,287 subagent_tokens, measured. Run 6 total so far 299,434.

2026-10-25 FocusPet #49 (run 6, second session) measured subagent_tokens, models from agent files:
reviewer (opus) 96,336 + 75,331 + 94,328 + 91,152; worker (haiku) 96,441 + 120,632 + 80,239.
Docs unit: worker (haiku) 71,302 + 72,015; reviewer (opus) 89,963 (resumed agent, second report looks cumulative; if per-run add 77,458).
Total 887,739 (965,197 if per-run). Run 6 both sessions 1,187,173 (1,264,631). Lead tokens, wall time not measured.
Result: merged as #53 (1ae5b1d); 3 rejections on the code unit, owner chose an inline fix (cbed0e5); bubble gap found only by hand test.
Lessons: (1) brief reviewers on the real user-facing path; (2) check PR state before a status page; (3) shared target dir; (4) flaky timing ratios; (5) classifier outage is not a refusal.

## Squirrow run 2

Measured subagent_tokens. Models from the agent files (worker haiku, reviewer opus). Lead tokens not measured.

| Unit | Role | Model | subagent_tokens | Verdict |
|---|---|---|---|---|
| #55 kind filter, round 1 | worker | haiku | 62,990 | report delivered |
| #55 kind filter, round 1 | reviewer | opus | 30,089 | CHANGES_REQUESTED (Welcome hidden for an empty project while a chip is on) |
| #55 kind filter, round 2 (rework) | worker | haiku | 46,864 | report delivered |
| #55 kind filter, round 2 | reviewer | opus | 25,782 | APPROVED (non-blocking: add empty-project test without a project switch; added inline by Lead) |
| #56 backup nudge, round 1 | worker | haiku | 61,309 | report delivered |
| #56 backup nudge, round 1 | reviewer | opus | 33,604 | APPROVED (non-blocking: clock-skew guard, failed-export test added inline by Lead) |
| #56 nudge rework (first-seen key, owner change request) | worker | haiku | 78,038 | report delivered; npm test failed in worktree (junction), verified by Lead after real npm ci |
| #57 real tokens, round 1 | worker | haiku | 119,509 | report delivered |
| #56 nudge rework (first-seen key) | reviewer | opus | 28,963 | APPROVED (non-blocking: StrictMode dev double-write, import-path test) |
| #57 real tokens, round 1 | reviewer | opus | 58,173 | APPROVED (non-blocking: narrow sw regex + guard tests -> hardening worker dispatched) |
| #57 sw hardening (narrow regex, guard tests) | worker | haiku | 37,489 | report delivered; Lead verified 138 tests, build, format, regex vs real dist |
| #57 sw hardening | reviewer | opus | 25,054 | APPROVED (non-blocking: second residual false positive like ./my-settings.js, accepted) |
| #58 saved picks, round 1 | worker | haiku | 138,747 | report delivered; Lead verified 175 tests, build, format |
| #58 saved picks, case-insensitive names (owner answer) | worker | haiku | 37,126 | report delivered |
| #58 saved picks, round 1 | reviewer | opus | 81,359 | CHANGES_REQUESTED (blocked-upgrade hang; Load+Auto-pick untested) |
| #58 saved picks, round 2 rework (blocked tab, tests, createdAt, empty names, replaceAll abort) | worker | haiku | 107,835 | report delivered; Lead verified 187 tests, build, format |
| #58 saved picks, round 2 | reviewer | opus | 35,217 | APPROVED (non-blocking: setError(null) can clear a superseded notice; listed in PR risks) |
| #59 import from chat, round 1 | worker | haiku | 156,959 | report delivered; Lead verified 258 tests, build, format |
| #59 import from chat, round 2 rework (loose text, tech names, Enter) | worker | haiku | 99,635 | report delivered; Lead verified 277 tests, build, format |
| #59 import from chat, round 1 | reviewer | opus | 71,606 | CHANGES_REQUESTED (colon split corrupts text; empty fence drops line; silent caps; Enter test and Back untested) |
| #59 import from chat, round 2 rework (5 findings + 3 extras) | worker | haiku | 114,771 | report delivered; Lead verified 303 tests, build, format |
| #59 import from chat, round 2 | reviewer | opus | 61,829 | CHANGES_REQUESTED (paste over cap with no line break imports nothing and says Nothing to import) |
| #59 import from chat, round 3 rework (reject giant single line + 3 extras) | worker | haiku | 69,292 | report delivered; Lead verified 312 tests, build, format, probe |
| #59 import from chat, round 3 | reviewer | opus | 41,968 | APPROVED (non-blocking: README wording for the single-line refusal; misleading refusal text on a contrived blank-prefix input) |

Squirrow run 2 totals (measured subagent_tokens): workers (haiku) 1,130,564; reviewers (opus) 493,644; total 1,624,208 across 24 reports. Per unit: #55 165,725; #56 201,914; #57 240,225; #58 400,284; #59 616,060. Lead tokens and wall time not measured. Merged as #60, #61, #64, #65, #66.

### Squirrow run 2 follow-ups (#67 overlay drag-close, #68 fence unwrap, #71 wrapped paragraphs, #63 CI check)

| Unit | Role | Model | subagent_tokens | Verdict |
|---|---|---|---|---|
| #67 overlay drag-close, round 1 | worker | haiku | 50,186 | report delivered; Lead verified 318 tests, build, format |
| #67 overlay drag-close, round 1 | reviewer | opus | 28,116 | CHANGES_REQUESTED (existing saving test passes vacuously: click with no mousedown) |
| #67 overlay drag-close, round 2 rework (saving test, reset test) | worker | haiku | 38,723 | report delivered; Lead verified 319 tests, build, format, src unchanged |
| #67 overlay drag-close, round 2 | reviewer | opus | 24,419 | APPROVED (no blockers, no notes) |
| #68 fence unwrap, round 1 | worker | haiku | 87,001 | report delivered; Lead verified 340 tests, build, format; 3 existing tests edited (spec-driven) |
(#67 subtotal: workers 88,909; reviewers 52,535; total 141,444, merged as #69.)
| #68 fence unwrap, round 1 | reviewer | opus | 47,114 | CHANGES_REQUESTED (plaintext not pinned; indent strip untested; both test gaps, code correct) |
| #68 fence unwrap, round 2 rework (3 tests) | worker | haiku | 49,660 | report delivered; Lead verified 351 tests, build, format, src unchanged |
| #68 fence unwrap, round 2 | reviewer | opus | 27,965 | APPROVED (non-blocking: backtick rule duplicated at two places, both now tested) |
(#68 final: workers 136,661; reviewers 75,079; total 211,740, merged as #70.)
| #71 join wrapped paragraph lines, round 1 | worker | haiku | not measured | STOPPED by the Lead mid-run at the owner's request (the owner saw it near 200k in their view, unverified; no usage figure was returned for the killed agent). Never reported; Lead verified the tree after stopping: one stale app test fixed inline, 380 tests, build, format |
| #71 join wrapped paragraph lines, round 1 | reviewer | opus | 93,002 | CHANGES_REQUESTED (5 behaviors untested: closing marks after a sentence end, italic in label title, italic in fence title; code correct) |
| #71 join wrapped paragraph lines, round 2 rework (5 tests, e.g./i.e., tidy-ups) | worker | haiku | 68,704 | report delivered; Lead verified 390 tests, build, format |
| #71 join wrapped paragraph lines, round 2 | reviewer | opus | 36,491 | APPROVED (non-blocking: two doc-comment wordings, fixed inline by the Lead; no test for I.e. alone) |
(#71 subtotal: workers 68,704 measured + one stopped worker not measured; reviewers 129,493; total measured 198,197, merged as #72.)
| #63 CI check of the sw scan, round 1 | worker | haiku | not measured | STOPPED by the Lead at the owner's request ("stop here for now") before it reported; no usage figure was returned for the killed agent. Work left uncommitted on branch ci/check-sw-scan, unverified. |
| #63 CI check of the sw scan, round 1 | reviewer | opus | 32,122 | CHANGES_REQUESTED (fake cache accepted URLs missing from dist; a real install would fail while the check stays green) |
| #63 CI check of the sw scan, round 2 rework (missing-URL rejection, dist message) | worker | haiku | 35,882 | report delivered; Lead verified 397 tests, build, check:sw exit 0, format |
| #63 CI check of the sw scan, round 2 | reviewer | opus | 27,614 | APPROVED (non-blocking: CLI block untested; percent-encoded names; first bad URL only) |
(#63 final: workers 35,882 measured + one stopped worker not measured; reviewers 59,736; total measured 95,618, merged as #73.)

Squirrow run 2 final totals (measured subagent_tokens): workers (haiku) 1,460,720; reviewers (opus) 810,487; total 2,271,207 across 40 reports, 38 measured. Five units 1,624,208 (#55 to #59); follow-ups 646,999 (#67 141,444; #68 211,740; #71 198,197; #63 95,618). Not measured: the two stopped workers (the first on #71 and the first on #63) and the Lead. Wall time not measured. Merged as #60, #61, #64, #65, #66, #69, #70, #72, #73.
