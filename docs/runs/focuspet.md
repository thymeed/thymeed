# FocusPet

Desktop companion app with local LLM chat. Private project: feature, architecture and eval detail are left out on purpose; the process and cost data stay.

**Repo:** Private repo.

Figures are measured `subagent_tokens` unless a line says otherwise. The Lead (main chat) is not counted unless stated. Nothing here is estimated; missing figures say "not measured".

## Runs

### Tool eval: calculator and weather tools (2026-10-22)

Two tools added, with a before and after eval, merged as one PR. The eval runs 15 prompts over 3 seeds (45 trials per run) and scores each reply.

| Run | Score | Strict re-score |
| --- | --- | --- |
| Baseline | 24/45 | 22/45 |
| After | 42/45 | 40/45 |

The strict re-score applies a tighter rule to the same saved replies, without calling the model again. A later wording fix brought the strict score to 42/45 as well. Run-to-run variation was not measured. Agent tokens for this work are in the Cost section.

### Run 3: agenda, units 1-2 (2026-10-24)

Planner (Sonnet) ran two units. Subagent tokens are each agent's own report; planner tokens not measured.

**Setup:** my agent team (~/.claude/agents), rework cap 3. **Wall time:** ~14 min (the planner agent's reported duration, 836 s).

| Unit | Agents | Subagent tokens | Rework | Status |
|---|---|---|---|---|
| 1. Verify a follow-up fix on main | researcher (haiku) 59,457 (no shell, inconclusive); worker (haiku) 42,917 ran the verify script on a worktree: all checks passed | 102,374 | 0 | verified, merged |
| 2. Text-filter branch | reviewer (opus) round 1 64,809 CHANGES_REQUESTED (3 findings); worker (haiku) 86,617; reviewer (opus) round 2 58,484 APPROVED | 209,910 | 1 round | approved, merged |
| Overhead | researcher (haiku) plan summary 36,122 (unneeded) | 36,122 | n/a | n/a |
| **Total** | 6 agents: 2 researcher (haiku), 2 worker (haiku), 2 reviewer (opus) | 348,406 (measured) | 1 | |

Round-1 finding kinds on unit 2 (3): a performance defect (quadratic on whitespace, 34 s on 50k spaces); a rule too broad (dropped legitimate text); deletion outside the intended scope.

#### Lessons

- **A researcher with no shell is a wasted dispatch for anything that needs commands.** It cost 59,457 tokens and came back inconclusive.
- **Do not dispatch a summary nobody asked for.** The unneeded plan summary cost 36,122 tokens. Together these two cost 95,579 tokens, about 27% of the run's 348,406.
- **Fix:** the planner sends anything that needs commands straight to the worker.

### Run 4: scorer unit and seeded scenario unit (2026-10-24)

Planner (Sonnet) ran one unit on a scorer-only branch. Subagent tokens are each agent's own report; planner tokens not measured.

**Setup:** my agent team (~/.claude/agents), rework cap 3. **Wall time:** not measured.

| Unit | Agents | Subagent tokens | Rework | Status |
|---|---|---|---|---|
| 1. Eval scorer, tests, rescore, baseline | worker (haiku) r1 132,555; reviewer (opus) r1 70,229 CHANGES_REQUESTED (scorer missed several input forms); worker (haiku) r2 101,649; reviewer (opus) r2 64,185 APPROVED | 368,618 (measured) | 1 round | merged |

**Unit 2: seeded eval scenario.** Planner (sonnet) subagent, then worker and reviewer (opus). Tokens: planner subagent 134,683 (measured, stopped by the owner after about 35 minutes); worker and reviewer tokens not measured. The planner never handed back a report, and the opus round-2 review was stopped by the owner after about 15 minutes because it ran too long. Round 1 review found that the first fixture copied or closely paraphrased the owner's real stored notes; that run was withdrawn and the fixture rewritten from scratch. The Lead then ran an independent overlap check against the local data folder (0 matches, with a positive control) and squashed the history to one commit before pushing, so the withdrawn fixture never reached the remote. Status: merged (eval only).

**Lessons (Run 4):**
- **Check eval fixtures for private data.** The first seeded fixture copied the owner's real notes. Review caught it, the run was withdrawn, and the history was squashed before pushing.
- **Time-box reviewers in the brief.** Two long reviews were stopped by the owner at about 15 minutes, which left the scenario unit without a finished round-2 review.

### Run 5: text-rule unit (2026-10-24)

**Setup:** first run with the Lead setup (the main chat plans), so effectively test 7. My agent team (~/.claude/agents), rework cap 3. Subagent tokens are each agent's own report; Lead tokens not measured. Wall time: not measured.

| Unit | Agents | Subagent tokens | Rework | Status |
|---|---|---|---|---|
| Rule, backstop, note handling, eval | worker (haiku) 125,044; reviewer (opus) r1 63,383 CHANGES_REQUESTED; seeded-eval worker (haiku) 55,766; worker (haiku) rework 109,687; reviewer (opus) r2 60,918 APPROVED; seeded-eval worker (haiku) 49,715 | 464,513 (measured) | 1 round | see Outcome below |

Round-1 blocker: the backstop deleted ordinary replies (a false-positive defect). Fix: narrowed the rule. One owner call on how to handle stored notes; later reversed, because write-time stripping destroyed real notes, so notes are now stored as written.

Update (same day, later rounds, measured subagent tokens): the run continued through five more review rounds, 1,128,967 measured subagent tokens in all up to round 5 (rework workers 145,092 + 131,642 + 165,566; reviewers round 3 70,756, round 4 72,460, round 5 78,938; plus the 464,513 above); the round 6 worker, its review and the page/doc work are not yet counted. Rounds: opus CHANGES_REQUESTED in rounds 1, 4 and 5 (the third hit the cap and went to the owner, who chose a narrower rule). Design changed twice on review. Main-chat (Lead) tokens not measured; wall time not measured.

#### Outcome

Merged as one PR on 2026-10-24. Final design: a prompt rule, a narrow reply backstop and a read-only note filter. Notes are stored as written and nothing is deleted.

Final eval scores (scorer v3.1, seeds 1 to 5): clean 89/95 to 94/95; seeded 42/95 to 66/95. The seeded gain came from the prompt rule and the reply backstop; the read filter contributed nothing to the eval. Owner hand test passed. One unrelated routing gap was found and filed.

The final review pass found the reply backstop deleting factual answers. Fixed with 11 keep cases shown red before the change. No further review round was run.

#### Lessons

- **Review catches what the worker's tests can't.** The first reviewer round caught a real blocker: the worker's keep list had only easy cases, and the backstop deleted ordinary replies.
- **Approvals weren't final.** Rounds 2 and 3 were APPROVED, but later owner and outside review reopened the work both times. The cap counts rejections, not rounds, so the unit ran 6 review rounds and 1.1M+ subagent tokens, about 3x Run 3. Next: also cap total rounds per unit.
- **Fuzzy text rules eat rounds.** Rounds 3 to 5 went to tuning one heuristic. When a rule needs a third rework, stop and pick the smallest safe version.
- **No data loss beat a better score.** Stripping notes on write lifted the seeded score to 88/95 but destroyed real progress notes. The owner chose read-only notes and a narrow read filter instead, accepting 66/95. The reviewer's data-loss rule decided the design.
- **The eval caught what unit tests didn't.** Unit checks passed throughout. The seeded scenario exposed the memory loop, and hand tests found leaks the scorer missed.
- **The rejection cap worked.** The third rejection stopped the loop and brought the decision to the owner instead of a fourth automatic round.

#### 2026-10-24 addendum: final pass (round 6)

Lead (sonnet) ran one final unit before the PR. reviewer (opus): CHANGES_REQUESTED, 74,109 subagent tokens. worker (haiku, worktree): fixed the backstop that deleted factual answers, 109,276 subagent tokens. Owner capped it at this run, so no re-review; Lead re-ran the verify script (318/318) and the selftest instead. Total 183,385 measured subagent tokens. Push needed a force-with-lease because the branch had been rebased onto main.

Lessons (final pass):
- **Probe, don't just run the suite.** All 298 gates were green while the backstop still deleted factual answers. The reviewer found it by pushing about 50 ordinary sentences through the filter. For text rules, put a list of ordinary sentences in the brief, not only the leaks to catch.
- **Check the review base.** Local `main` was stale, so the diff pulled in three merged PRs. The reviewer used the real base. Run `git fetch` and name the base commit in the reviewer brief.
- **A rebased branch needs a pinned force-push.** The remote held the pre-rebase copy of the same commits, so a plain push was rejected. `--force-with-lease=<branch>:<sha>` was the safe form, and only because no PR existed yet.
- **A hand test through the app can hide the raw reply.** Re-sending the prompts through the eval's exported request showed raw against filtered output and cleared the filter.
- **Do the log and cost entries in the same turn as the numbers.** Measured tokens (74,109 and 109,276) were written the moment both agents had reported.

### Run 6: routing unit (2026-10-24 to 10-25, merged)

Round 1: worker (haiku) 104,517 subagent tokens; reviewer (opus) 78,630, CHANGES_REQUESTED (3 blockers: a wrong-path defect, a false-positive defect, a missing prompt rule). Owner chose a behaviour option and added a backstop. Round 2 worker (haiku) stopped early, nothing committed: 116,287 measured subagent tokens spent. Run total so far 299,434 (104,517 + 78,630 + 116,287).

#### Run 6 continued (2026-10-25): shipped, plus a docs unit

Lead (sonnet). Measured subagent tokens, models from the agent files (worker haiku, reviewer opus):

| Unit | Agent | Tokens | Verdict / result |
|---|---|---|---|
| Code review | reviewer | 96,336 | CHANGES_REQUESTED (missing prompt rule; claim-filter hole; patterns too loose) |
| Rework 1 | worker | 96,441 | committed by Lead (the worker's shell was blocked) |
| Code review | reviewer | 75,331 | CHANGES_REQUESTED (claim-filter hole still open) |
| Rework 2 | worker | 120,632 | committed |
| Code review | reviewer | 94,328 | CHANGES_REQUESTED, third rejection (remaining hole, 3/150) |
| Lead inline fix, owner's choice | Lead | not measured | committed |
| Display fix | worker | 80,239 | committed |
| Review of the last two commits | reviewer | 91,152 | APPROVED |
| Docs refresh | worker | 71,302 | committed |
| Docs review | reviewer | 89,963 | CHANGES_REQUESTED, then APPROVED after a rework (see note) |
| Docs rework (PR merged, red main CI) | worker | 72,015 | committed |

Total this session 887,739 measured subagent tokens. The docs reviewer was resumed for round 2; its two reports were 77,458 and 89,963 and the second looks cumulative (4 more tool calls in 49 s), so I count 89,963 once. If it is per-run, the total is 965,197. Run 6 across both sessions: 1,187,173 (or 1,264,631 on that reading). Lead tokens and wall time not measured. Two worker/reviewer shell calls were blocked by an auto-mode classifier outage; no tokens are known for those.

Outcome: merged 2026-10-25. Final gates all green, and live probes improved from 1 of 5 to 30 of 30. `main` CI went red on the push run from a flaky timing-ratio assertion (3.70 against a limit of 3.5; the PR run passed it at 2.21). Docs branch approved on its second review, not pushed. Owner hand tests the final build on 2026-10-26.

Lessons (run 6, second session):
- **Hand test beat every probe and reviewer.** One user-facing path still showed raw model text; the filter only fed history and voice. Three reviewer rounds and 150 simulated conversations called the filter function directly, so none saw it. Brief reviewers on the path the user sees, not the function.
- **The owner's choice at the cap worked, but needed a review after.** The third rejection went to the owner, who chose an inline fix. It then got its own review with the next unit, and that review passed.
- **Check the PR state before committing a status page.** The docs commit went stale 12 seconds later when the PR merged; the reviewer caught it and it cost a second docs round.
- **Worktrees share one target dir.** The owner's build in the main checkout overwrote the exe built from the branch, so a lag comparison ran `main`, not the branch. Now a trap in CLAUDE.md.
- **Timing ratio assertions flake on shared runners.** A ratio of two ~10 ms measurements failed once on CI. Fix is a median of several runs or a larger input.
- **The classifier outage looked like a refusal.** Eight "no verdict" errors in a row blocked commits and agent launches; switching to manual approval worked. Do not loop on it.

#### Run 6 summary

| Part | Tokens | Review result |
|---|---|---|
| Code unit (workers 5 + reviewers 5, both sessions) | 953,893 | 4 CHANGES_REQUESTED (78,630; 96,336; 75,331; 94,328), then APPROVED (91,152). The third rejection went to the owner, who chose an inline fix. |
| Docs unit | 233,280 | 1 CHANGES_REQUESTED, then APPROVED after a rework. |
| **Run 6 total** | **1,187,173** (1,264,631 if the resumed docs reviewer's two reports are per-run) | |

- **Review rounds:** 5 on the code unit, 2 on the docs unit.
- **Lead tokens:** not measured.
- **Wall time:** not measured. Only dates are recorded (2026-10-24 to 10-25).

### Run 10: five-issue batch (2026-10-29, closed by the owner at 98% usage)

- Scope (owner): five issues. Lead = Sonnet main chat; workers and the advisor passes ran through `worker`/`reviewer` agents.
- Merged: six PRs in total. One was test-only because the issue was already fixed on main.
- Review rounds per unit: 1; 2; 1; 5 (rounds 3 and 4 fixed inline by the Lead at the owner's request); 2; 3 (round 1 approved, rounds 2 and 3 were a style rule and body wording).
- Follow-ups filed: four. Not started: one issue.
- All PR gates were run locally and posted as PR comments because CI was blocked by a GitHub billing message the whole run.
- Cleanup: 37 stale local branches and 9 worktrees removed after checking each tip against its merged PR; left for the owner: 1 worktree with an uncommitted diff, 1 with images, 4 local branches with no PR.
- Lessons: (1) the Lead created sibling worktrees outside the project folder against CLAUDE.md; they now go under .worktrees/. (2) A reviewer told to render a panel started preview servers and one outlived the review; later briefs forbid servers and windows during the owner's hand test. (3) `String.replace` with a replacement containing `$'` corrupted a PR body; use split/join or a function. (4) The owner's hand test caught what no gate did (a routing gap); a live-run checklist belongs in the brief for any user-facing routing change. (5) One issue was already fixed; check the issue against main before briefing a worker. (6) A worker's brief must say when a finding could be a premise problem. (7) Resumed agents report cumulative tokens, so per-round splits are not measured. (8) The Lead stopped at the third request on one unit and the owner chose inline fixes, which worked but cost two extra review rounds.

### Run 12: persona rewrite, voice eval, local arithmetic (2026-10-31 to 2026-11-01, in progress)

- Units: (1) a small text filter: merged (CI passed in 3m23s). (2) A voice eval (fixture, scorer with eleven flags, runner, baseline run): merged (CI passed in 3m31s). Baseline: 0 of 45 pass. (3) A new system prompt (worktree, uncommitted, parked). (4) A local arithmetic parser (208 checks): worker done, reviewer round 1 running.
- Agents and models: worker x6 (haiku per agent file), reviewer x9 completed plus 1 running (opus per agent file). No researcher. The Lead made several inline edits and rebuilt the exe for the owner's hand tests.
- Review rounds: filter round 1 CHANGES_REQUESTED, round 2 APPROVED, round 3 APPROVED after the owner extended the rule. Voice eval round 1 CHANGES_REQUESTED (6), round 2 CHANGES_REQUESTED (3), round 3 CHANGES_REQUESTED (1); that was the third CHANGES_REQUESTED, so the Lead stopped and asked; the owner chose an inline fix plus one narrow round 4, which APPROVED. New prompt round 1 CHANGES_REQUESTED (invented facts, a wrong sum, two unguarded rules), round 2 CHANGES_REQUESTED (the wrong sum sat on the default path). Arithmetic round 1: running.
- Owner decisions: scope and order of the work; build the deterministic arithmetic parser before shipping the prompt; fix one line in its own PR; a fourth review round for the scorer; hand tests are the owner's; PR and commit text never says who ran what.
- Measured subagent tokens: workers 124,028 + 161,167 + 203,072 + 174,108 + 102,551 + 240,860 = 1,005,786; reviewers 59,344 + 54,942 + 95,587 + 128,129 + 112,235 + 113,341 + 95,044 + 73,349 + 101,039 = 833,010; total 1,838,796. Lead (main chat) tokens: not measured. Per-agent rows are in the Cost section of this file.
- Results: an unrecorded 3-seed run of the new prompt went from 33/45 to 37/45 after two fixes; not saved to a results file. The arithmetic parser agreed with the compiled implementation on 20,028 generated expressions (the worker's differential test; not yet re-run by the reviewer).
- Lessons: (1) A passing scorer does not mean the replies are right: 39/45 hid fabricated facts and a wrong sum; the reviewer and a hand read caught them. The scorer should get a correctness flag. (2) The Lead argued a tool covered a case without checking that tools are off by default; the reviewer read the code. Check defaults before relying on a path. (3) Prompt edits move the numbers unpredictably; the structural fix is a conditional rule in code. (4) An edit that removed a bullet glued the next one on and no test noticed; there is now a structure check. (5) A break-and-restore check silently did not apply twice (escaping): print "BREAK DID NOT APPLY" and check the diff before trusting a red run. (6) Briefs must forbid redirects to /tmp; two stray temp files came from that. (7) A PR body should say what was run and what it showed, not who ran it.
- Deviations: system temp. The Lead wrote one file to /tmp (an eval redirect) and deleted it at once. A worker wrote one 119-byte file under the system Temp folder (a format-check redirect); deleted after the owner said yes. A reviewer saw 3 GUID-named .tmp files appear in system temp; nobody could show they were ours, and they were left alone. A worker's first verify run hung and left a node process running for hours; the Lead stopped it after checking its command line. Two node processes belonging to another session were not touched. The Lead built and launched the release exe from a worktree for the owner's hand tests and closed it each time.
- Not done yet: the arithmetic reviewer verdict and merge; the new prompt's recorded eval, hand test and PR; a few follow-up units; one docs PR.

## Cost

### 2026-10-22, first batch

| Task | Role | Model | Tokens | Tool uses | Wall time | Status |
| --- | --- | --- | --- | --- | --- | --- |
| Tools and eval | Owner A | sonnet | 287,547 | 102 | 28m 3s | done |
| Art change | Owner B | sonnet | 267,154 | 97 | 13m 34s | done |
| Voice change | Owner C | sonnet | 129,687 | 21 | 2m 24s | done |
| Voice diff review | Reviewer | sonnet | 76,884 | 4 | 28s | done |

Finished so far: 761,272 subagent tokens.

### Notes

- All subagents run on sonnet per `pstack-models.md`, in the lean setup (no swarm lanes).

### 2026-10-22 (second batch): issue fixes

| Task | Role | Model | Tokens | Tool uses | Wall time | Status |
| --- | --- | --- | --- | --- | --- | --- |
| CI split: fast PR, full main | Worker | haiku | 119,793 | 29 | 4m 30s | done, in review |
| Unit fix A | Worker | haiku | 95,658 | 31 | 4m 26s | done, in review |
| Compiler warnings | Worker | haiku | 78,028 | 17 | 4m 16s | done, in review |
| Debug-only log | Worker | haiku | 95,566 | 27 | 5m 52s | done, in review |
| Review: compiler warnings | Reviewer | opus | 71,757 | 4 | 30s | done, ship |
| Review: CI split | Reviewer | opus | 79,292 | 6 | 1m 4s | done, fix first (cache), fixed |
| Review: unit fix A | Reviewer | opus | 79,616 | 9 | 53s | done, fix first (precision), fixed |
| Review: debug log | Reviewer | opus | 74,920 | 5 | 40s | done, ship |

Finished in this batch: 694,630 subagent tokens (4 haiku workers 389,045 + 4 opus reviewers 305,585). The parent session is not counted in the rows above.

#### Advisor passes before opening PRs (opus, read-only)

| Pass | Target | Tokens | Tool uses | Result |
| --- | --- | --- | --- | --- |
| 1 | CI PR | 85,434 | 6 | fix first (cache-key story wrong, dev cache would not save) |
| 1 | warnings branch and body | 71,702 | 4 | fix first (metadata only) |
| 1 | debug-log branch and body | 74,821 | 5 | fix first (body wording) |
| 1 | unit fix A branch and body | 76,544 | 4 | fix first (body wording) |
| 2 | CI PR | 78,571 | 6 | one sentence, fixed |
| 2 | three bodies | 79,117 | 6 | one wording fix |

Advisor total: 466,189 tokens. PRs opened afterwards: four.

#### Session totals from `ccusage session`, read at the end of 2026-10-22

| Session | Last activity | Models | Output tokens | Cost |
| --- | --- | --- | --- | --- |
| First (fixes, workers) | 10:15Z | opus-5-5 $1.92, haiku-5-5 $0.24 | 66,046 | $2.16 (8,468,688 tokens) |
| Second (reviews, PRs, issues) | 11:32Z | sonnet-5-5 $4.70, opus-5-5 $2.82 | 125,887 | $7.51 (29,814,107 tokens) |

- The second session shows a sonnet row, which is the parent session. The first session showed none, so its $2.16 was the subagents only and the parent's turns in it are missing.
- Combined for the two sessions: about $9.67 and 38.3M tokens, mostly cache reads. Treat it as a floor for the first session.
- Only these two rows were copied; no paths or other sessions.

#### Day total from ccusage, 2026-10-22 (all projects, not FocusPet alone)

| Day | Models | Input | Output | Cache create | Cache read | Total tokens | Cost |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 2026-10-22 | haiku-5-5, opus-5-5, sonnet-5-5 | 1,180 | 418,590 | 1,937,338 | 115,888,146 | 118,245,254 | $20.55 |

- This covers every project and session that day, including the parent session and the subagents above. It cannot be split into a per-project or parent-session figure.
- Cache reads are 98% of the tokens, so the token total says little; the cost is the usable number.

#### First session, from `ccusage session` (read 2026-10-22; last activity 10:15Z, so later turns may be missing)

| Model | Input | Output | Cache create | Cache read | Cost |
| --- | --- | --- | --- | --- | --- |
| opus-5-5 | 44 | 14,063 | 275,406 | 1,293,463 | $1.92 |
| haiku-5-5 | 158 | 51,983 | 384,633 | 6,448,938 | $0.24 |
| Total | 202 | 66,046 | 660,039 | 7,742,401 | $2.16 (8,468,688 tokens) |

- ccusage lists only opus and haiku for this session. The parent ran as sonnet-5-5 and no sonnet row appears, so this is probably the subagent transcripts alone and the parent's own cost may be missing. Treat $2.16 as a floor, not the full session cost.
- Only this session's row was copied; the 69 other sessions in the output were left out.
- ccusage warned that pricing is missing for two cloud-hosted models, so the cost excludes those models.

### 2026-10-22 (third batch): eval and fix units

Parent session is sonnet-5-5; workers are haiku @medium, advisors opus. Subagent tokens only; the parent is not counted.

| Task | Role | Model | Tokens | Tool uses | Wall time | Status |
| --- | --- | --- | --- | --- | --- | --- |
| Eval fixture + baseline | Worker | haiku | 157,133 | 56 | 5m 6s (resumed once) | done, in advisor review |
| Wording cleanup | Worker | haiku | 178,507 | 64 | 6m 47s | done, in advisor review |
| Review: eval PR (pass 1) | Advisor | opus | 95,169 | 9 | 1m 39s | done, fix first (routing prompt, fidelity holes, meta) |
| Review: wording PR (pass 1) | Advisor | opus | 112,622 | 10 | 1m 37s | done, fix first (two misses, scope wording) |
| Fix round: wording PR | Worker | haiku | 107,700 | 17 | 1m 39s | done, pushed |
| Fix round: eval PR | Worker | haiku | 188,777 | 52 | 7m 34s | done |
| Review: eval PR (pass 2) | Advisor | opus | 114,089 | 22 | 3m 11s | done, ship after 3 fixes |
| Final small fixes: eval PR | Worker | haiku | 125,862 | 30 | 3m 57s | done, pushed (see incident) |
| Display unit | Worker | haiku | 108,429 | 31 | 3m 58s | done, in advisor review |
| Review: display PR (pass 1) | Advisor | opus | 83,935 | 8 | 54s | done, ship after 3 small fixes |
| Fix round: display PR | Worker | haiku | 89,054 | 13 | 47s | done, pushed |
| Investigate a layout bug | Worker | haiku | not reported | | | stopped by me (headless Edge relaunches, error sound); no diagnosis |
| Live window check | Worker | haiku | not reported | | | stopped; measured by me over the debug port instead |
| Routing + measurement | Worker | haiku | 144,404 | 56 | 3m 38s | done, in advisor review |
| Review: routing PR (pass 1) | Advisor | opus | 93,374 | 11 | 1m 24s | done, fix first (rule too broad, notes overclaim) |
| Fix round: routing | Worker | haiku | 131,803 | 28 | 4m 10s | done, pushed |
| Panel layout fix | Worker | haiku | 113,985 | 26 | 3m 21s | done, in advisor review |
| Review: layout PR (pass 1) | Advisor | opus | 86,521 | 9 | 1m 8s | done, fix first (same bug elsewhere) |
| Measure on the fix build | Worker | haiku (low effort) | 97,163 | 10 | 2m 23s | done |
| Fix + tighten gate | Worker | haiku (medium) | 120,822 | 25 | 3m 24s | done, pushed |

### 2026-10-22 (fourth batch): one follow-up fix

Parent session is sonnet-5-5; all code written inline by the parent (small change), one opus advisor. Subagent tokens only; the parent is not counted.

| Task | Role | Model | Tokens | Tool uses | Wall time | Status |
| --- | --- | --- | --- | --- | --- | --- |
| Review: follow-up PR (pass 1) | Advisor | opus (medium) | 80,589 | 10 | 36s | done, fix first (state-handling defects, regex gaps); fixed inline by the parent |
| Review: follow-up PR (pass 2, asked by owner) | Advisor | opus (medium) | 84,300 | 8 | 47s | done, fix first (one persistence defect); fixed inline; one older hole left, predates PR |

- Not delegated on purpose: the change is 3 files, about 25 lines, so a worker's cold start would cost more than writing it. Floor figure: the parent's own tokens are not included.
- Advisor total: 164,889 tokens over two passes (the second was an owner request, not the one-pass rule).
- Hand test round 1 failed; fixed inline in a fourth commit, retest passed. No third advisor pass (small change; owner asked for two).

### 2026-10-22 (fifth batch): eval measurement PR

Parent session is sonnet-5-5; script and notes written inline by the parent (about 180 lines plus notes, narrow scope), advisors opus. Subagent tokens only; the parent is not counted, so figures are a floor.

| Task | Role | Model | Tokens | Tool uses | Wall time | Status |
| --- | --- | --- | --- | --- | --- | --- |
| Review: measurement PR (pass 1) | Advisor | opus (medium) | 79,183 | 6 | 38s | done, fix first (eval settings differed from the app, unsanitised scoring, a false positive); fixed inline, baseline re-run |
| Review: measurement PR (pass 2) | Advisor | opus (medium) | 82,013 | 9 | 1m 2s | done, fix first (eval called a different endpoint than the app; scorer gaps); fixed inline, baseline re-run twice |

- Local model runs for the baselines are not billed tokens.
- A scripted edit chain failed once and committed a file deletion under a misleading message before I reset it (local, never pushed). Lesson: chain edits with `&&`, not `;`.
- Advisor total: 161,196 tokens over two passes (eval PR, so two per the rule).

### 2026-10-22 (sixth batch): CI cost change

Parent session is sonnet-5-5; workflow edits written inline by the parent (one file plus stale-text fixes), advisors opus. Subagent tokens only; the parent is not counted, so figures are a floor.

| Task | Role | Model | Tokens | Tool uses | Wall time | Status |
| --- | --- | --- | --- | --- | --- | --- |
| Review: CI PR (pass 1) | Advisor | opus (medium) | 81,145 | 6 | 56s | done, fix first (stale docs, a wrong figure, a missing compile check); fixed inline |
| Review: CI PR (pass 2) | Advisor | opus (medium) | 77,685 | 5 | 38s | done, fix first (a shared cache key would leave artifacts unsaved; imprecise figures); fixed inline with a separate job |

- Two earlier PRs were pushed the same day; neither was merged at the time of writing.
- Advisor total: 158,830 tokens over two passes.

### 2026-10-22 (seventh batch): behaviour fix PR

Parent session is sonnet-5-5; code, tests and eval run inline by the parent (about 25 lines of code plus tests and notes), advisor opus. Subagent tokens only; the parent is not counted, so figures are a floor.

| Task | Role | Model | Tokens | Tool uses | Wall time | Status |
| --- | --- | --- | --- | --- | --- | --- |
| Review: behaviour fix PR (pass 1) | Advisor | opus (medium) | 83,169 | 8 | 1m 15s | done, fix first (rule dropped legitimate text, quadratic on long lines, no false-positive tests); fixed inline, eval re-run |

- Three earlier PRs were merged by the owner before this batch.
- Local model runs (the after eval, 70 trials, run twice because the first had a dirty tree) are not billed tokens.
- Process slip: the first after-run recorded dirty=true because an uncommitted edit from another branch was carried over. Committed it and re-ran.
- Advisor total: 83,169 tokens (one pass, behaviour PR). Day total of advisor passes logged above: 164,889 + 161,196 + 158,830 + 83,169 = 568,084 tokens, a floor (parent sessions not counted).
- HANDOFF.md updated on a docs-only branch (skips CI).

### Run 5: text-rule unit (2026-10-24), first run with the Lead setup (main chat plans)

| Role | Model | Tokens | Result |
| --- | --- | --- | --- |
| Worker: rule, backstop, eval | haiku | 125,044 | done |
| Reviewer round 1 | opus | 63,383 | CHANGES_REQUESTED (blocker: false positives) |
| Worker: seeded eval, round-1 code | haiku | 55,766 | measured |
| Worker: rework + note handling | haiku | 109,687 | done |
| Reviewer round 2 | opus | 60,918 | APPROVED |
| Worker: seeded eval, final code | haiku | 49,715 | measured |

Measured subagent total: 464,513. Lead (main chat) tokens not measured. Wall time not measured. Models are from the agent files.

#### Run 5 continued (same day), measured subagent tokens

| Role | Model | Tokens |
| --- | --- | --- |
| Worker: read-time filter + fixes | haiku | 145,092 |
| Reviewer round 3 | opus | 70,756 (APPROVED) |
| Worker: subject rule | haiku | 131,642 |
| Reviewer round 4 | opus | 72,460 (CHANGES_REQUESTED) |
| Worker: read-only rule | haiku | 165,566 |
| Reviewer round 5 | opus | 78,938 (CHANGES_REQUESTED, third rejection, owner chose narrower rule) |

Subtotal 664,454; with the first part of Run 5 (464,513) 1,128,967. Round 6 worker, its review and doc work not yet counted. Lead tokens not measured.

2026-10-24 final pass: reviewer (opus) 74,109; worker (haiku) 109,276 subagent_tokens, measured. Total 183,385.
Lessons: (1) a green suite did not catch the backstop deleting factual answers; a reviewer probe of ordinary sentences did, so brief workers with a keep list of ordinary text. (2) Name the review base commit; a stale local main inflated the diff. (3) The worker pass was cheap (109,276) next to the reviewer (74,109) only because the owner capped it at one pass; uncapped rework on this unit ran 1.1M+ earlier. (4) Raw-vs-shown reply checks via the eval harness cost nothing in subagent tokens.

2026-10-24 routing unit round 1: worker (haiku) 104,517; reviewer (opus) 78,630 subagent_tokens, measured. Round 2 worker pending.
2026-10-24 routing unit round 2 worker (haiku, stopped early, no commit): 116,287 subagent_tokens, measured. Run 6 total so far 299,434.

2026-10-25 routing unit (run 6, second session) measured subagent_tokens, models from agent files:
reviewer (opus) 96,336 + 75,331 + 94,328 + 91,152; worker (haiku) 96,441 + 120,632 + 80,239.
Docs unit: worker (haiku) 71,302 + 72,015; reviewer (opus) 89,963 (resumed agent, second report looks cumulative; if per-run add 77,458).
Total 887,739 (965,197 if per-run). Run 6 both sessions 1,187,173 (1,264,631). Lead tokens, wall time not measured.
Result: merged; 3 rejections on the code unit, owner chose an inline fix; a display gap found only by hand test.
Lessons: (1) brief reviewers on the real user-facing path; (2) check PR state before a status page; (3) shared target dir; (4) flaky timing ratios; (5) classifier outage is not a refusal.

### Run 10 (2026-10-29), raw per-notice numbers
Resumed agents report one number per notice and it appears cumulative (tool_uses rise); per-round splits are "not measured", so none are summed. Total: not measured.

| Unit | Agent | Model | subagent_tokens (raw notices) |
| --- | --- | --- | --- |
| Unit 1 | worker | haiku | 99,038 |
| Unit 1 | reviewer, resumed for the advisor pass | opus | 53,526, then 60,025 |
| Unit 2 | worker r1 | haiku | 147,612 |
| Unit 2 | worker r2 (fresh) | haiku | 164,168 |
| Unit 2 | reviewer, resumed 3 times | opus | 87,043, then 117,063, then 127,705 |
| Unit 3 (test-only) | worker | haiku | 87,751 |
| Unit 3 | reviewer (diff + body in one pass) | opus | 67,104 |
| Unit 4 part 1 | worker r1, resumed once | haiku | 179,316, then 198,049 |
| Unit 4 part 1 | worker r2 (fresh) | haiku | 151,096 |
| Unit 4 part 1 | worker r3 (fresh) | haiku | 138,772 |
| Unit 4 part 1 | reviewer, resumed 5 times | opus | 114,118; 153,759; 180,971; 197,599; 207,546; 215,951 |
| Unit 5 part 1 | worker | haiku | 90,130 |
| Unit 5 part 1 | reviewer | opus | not measured (stopped before reporting) |

Lead (main chat) tokens: not measured. Unit 4 round 3 and 4 fixes were done by the Lead inline.

#### Run 10 additions (raw per-notice numbers; cumulative-vs-segment not established, none summed)

| Unit | Agent | Model | subagent_tokens (raw notices) |
| --- | --- | --- | --- |
| Unit 5 part 1 | worker | haiku | 90,130 |
| Unit 5 part 1 | reviewer 1 (stopped at the usage limit) | opus | not measured |
| Unit 5 part 1 | reviewer 2, resumed once | opus | 72,844, then 86,161 |
| Unit 4 part 2 | worker, resumed once | haiku | 167,466, then 193,049 |
| Unit 4 part 2 | reviewer, resumed 3 times | opus | 88,656; 100,962; 110,486 |

Lead (main chat) tokens: not measured. Run 10 total: not measured.

### Run 12, 2026-10-31 to 2026-11-01: filter, voice eval, new prompt, local arithmetic

Raw per-agent numbers as the completion notices reported them (the final notice is used where an agent sent an interim one). Wall time is rounded to whole seconds. One reviewer (arithmetic parser, round 1) was still running when this was written and is not included.

| Task | Role | Model | Tokens | Tool uses | Wall time | Status |
| --- | --- | --- | --- | --- | --- | --- |
| Text filter | worker | haiku | 124,028 | 28 | 187 s | done, merged |
| Filter review round 1 | reviewer | opus | 59,344 | 7 | 102 s | CHANGES_REQUESTED (a character lost) |
| Filter review round 2 | reviewer | opus | 54,942 | 7 | 90 s | APPROVED |
| Filter review round 3 | reviewer | opus | 95,587 | 18 | 125 s | APPROVED |
| voice eval: fixture, scorer, runner | worker | haiku | 161,167 | 26 | 287 s | done |
| voice eval review round 1 | reviewer | opus | 128,129 | 20 | 202 s | CHANGES_REQUESTED (6) |
| voice eval fix round 1 | worker (fresh) | haiku | 203,072 | 47 | 605 s | done |
| voice eval review round 2 | reviewer | opus | 112,235 | 17 | 227 s | CHANGES_REQUESTED (3) |
| voice eval fix round 2 | worker (fresh) | haiku | 174,108 | 55 | 394 s | done |
| voice eval review round 3 | reviewer | opus | 113,341 | 20 | 251 s | CHANGES_REQUESTED (1, scorer false positive) |
| voice eval review round 4 (narrow, owner-approved) | reviewer | opus | 95,044 | 12 | 140 s | APPROVED, merged |
| new system prompt | worker | haiku | 102,551 | 32 | 173 s | done, parked |
| new system prompt review round 1 | reviewer | opus | 73,349 | 18 | 218 s | CHANGES_REQUESTED |
| new system prompt review round 2 | reviewer | opus | 101,039 | 20 | 190 s | CHANGES_REQUESTED |
| local arithmetic parser | worker | haiku | 240,860 | 82 | 1072 s | done, in review |

Workers (6): 1,005,786. Reviewers (9): 833,010. Total measured subagent tokens: 1,838,796. Lead (main chat) tokens: not measured.

- Models are the model lines in ~/.claude/agents (worker haiku, reviewer opus); the notices do not report the model, so it is not independently measured.
- The arithmetic worker sent an interim notice (239,777 tokens, 934,741 ms) before its final one (240,860 tokens, 1,072,236 ms); only the final figures are counted.
