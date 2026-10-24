# Orchestration log (case studies)

Smoke tests of the agent team are in [agent-team-smoke-tests.md](agent-team-smoke-tests.md). Those six tests ran the planner-subagent version, not the Lead setup (the main chat plans, following `~/.claude/orchestration/Lead.md`). The first real run with the Lead setup is therefore effectively test 7: it is the first run recorded here under that setup, and nothing before it proves it.

## Run 1: Squirrow v1.1.0 (case study)

How one release was built with a multi-agent Claude Code workflow, with measured tokens, estimated cost, review findings and wall time for each unit of work.

**Setup:** one main Claude Code session planned and coordinated the run. Small units were done inline. The largest unit (Unit 3) went to Haiku workers in separate git worktrees, with Sonnet reviewers checking their work. There were no extra coordinator or verifier agents. The repo started as `thymeed/context-keeper` and was renamed to Squirrow in Unit 1.

| Unit | Issue / PR | Workers | Subagent tokens | Main-session tokens (ccusage estimate) | Cache-read share | Cost (ccusage estimate) | Rework (review findings fixed) | Wall time | Status |
|---|---|---|---|---|---|---|---|---|---|
| Planning | none | 1 Explore (haiku) | 72,287 (measured) | included in Unit 1 | n/a | n/a | 0 | n/a | done |
| 1 Rename | #38 / #39 | 0, inline | 0 | 4,592,786 (2,678,043 session A planning and build; 1,914,743 session B review fix and merge) | 95.2% (4,373,893 cache reads) | $1.16 ($0.70 + $0.46) | 2 (migration works only on the same web address; migration errors were not caught) | ~59 min (22:05 to 23:04 UTC, 2026-10-21) | merged |
| 2 PWA update prompt | #40 / #41 | 0, inline | 0 | 2,646,024 (session B, 4,560,767 at merge minus 1,914,743 at Unit 1 close) | 98.1% (2,596,428 cache reads) | $0.57 (rounded from raw $1.0351 - $0.4612, so the rounded parts look like $0.58) | 5 (3 code: other tabs left on a dead button, installing worker missed, effect re-ran every render; 2 PR text: half-typed-work claim, and a v4-users claim. The v4 correction was later disproven by a live two-tab test run by the owner: the page loads network-first, so v4 users do see the prompt. A reviewer's finding was wrong and real testing caught it; PR #41 got a correcting comment. The owner then reported the live two-tab update check as passed, which proves the update prompt works end to end; the agent did not run or observe it.) | ~18 min (23:04 to 23:22 UTC, 2026-10-21; start is approximate) | merged |
| Cleanup: og.png demo image | #42 / #43 | 0, inline | 0 | not measured separately; falls inside the next session delta (session total was 4,560,767 at Unit 2 merge) | n/a | n/a | 1 (the first screenshot was of a real private research project, caught before it was committed; replaced with a made-up demo project) | ~11 min (23:22 to 23:33 UTC, 2026-10-21) | merged |
| 3 Model budget + smart context | #44 / #45 | 4 haiku workers (1 lost to a deleted worktree), 3 sonnet reviewers | haiku workers 11,028,063 incl. cache (ccusage; 418,566 excluding cache per agent reports). Sonnet reviewers 183,027 excluding cache (agent reports) | 13,876,534 sonnet, covering Unit 3 plus the og.png cleanup (session total 29,465,364 minus Unit 2 close 4,560,767 minus haiku) | 96.6% (24,049,053 of 24,904,597 session delta, subagents included) | $3.42 ($3.01 sonnet, $0.41 haiku; session delta, includes og cleanup) | 12 fixed. Reviews 1 and 2 (4): auto-pick pinned its own earlier result; stale status text; weak and missing tests; stale status on project change. Owner's PR review (4): all-or-nothing task matching; unticked entries missing from the counts; Select all pinning everything; fixed model windows. Review 3 (4): a stale hidden tick; common-word weighting; uncapped task terms; uncapped custom window. (An earlier version of this row said 8 and grouped these loosely.) 2 known and left (slow on thousands of entries; unreachable tiny budget). Plus 1 process loss: a worker killed when its worktree was deleted, 83,514 tokens redone. | ~30 min (23:36 to 00:06 UTC, 2026-10-21/22; the docs unit started at 00:03, so the last 3 minutes overlap) | merged 2026-10-22 UTC (8:06 PM EDT, Oct 21) |
| Docs + log wrap-up | #46 / #47 | 0, inline | 0 | 4,067,953 (session B, 33,533,317 at 00:16:52 UTC minus 29,465,364 at 00:03 UTC) | 99.3% (4,040,727 cache reads) | $0.59 ($5.04 - $4.45) | 0 | ~14 min (00:03 to 00:17 UTC, 2026-10-22; includes the cost.md edits) | merged 2026-10-22 UTC (8:17 PM EDT, Oct 21) |
| Build stamp + release 1.1.0 | #48 / #49, #50 / #51 | 0, inline | 0 | 7,609,850 (session B, 41,143,167 at 00:36:10 UTC minus 33,533,317 at 00:16:52 UTC) | 99.4% (7,562,189 cache reads) | $1.07 ($6.11 - $5.04) | 2 (from the PR #49 review: the worker's own code was left out of the hash; changelog wording said every release) | ~19 min (00:17 to 00:36 UTC, 2026-10-22; includes cost.md fixes and release Q&A) | merged; v1.1.0 tagged and released 2026-10-22 UTC (8:36 PM EDT, Oct 21) |
| **Run total** | #38 to #51 | 0 inline + 1 Explore + 4 haiku workers, 3 sonnet reviewers | haiku workers 11,028,063 incl. cache; planning Explore 72,287; sonnet reviewers 183,027 excl. cache (agent reports) | 43,821,210 across sessions A and B (includes subagents) | 97.3% (42,622,290 cache reads) | $6.81 ($6.11 + $0.70) | 22 (U1 2, U2 5, cleanup 1, U3 12, docs 0, stamp + release 2) | ~2 h 31 min (22:05 to 00:36 UTC) | all units merged; v1.1.0 released; readings at 00:36:10 UTC |

### Lessons

- **Review catches real bugs, and real testing catches wrong reviews.** 22 findings were fixed before merge, but one reviewer finding (the v4-users claim in Unit 2) was wrong, and a live two-tab test disproved it.

- **Small units stay cheap.** The inline units cost $0.57 to $1.16 each. The one unit with parallel workers cost $3.42 and lost one worker to a deleted worktree.

- **Check every image before it's committed.** The first demo screenshot showed a private project and had to be replaced.

### Method

Token figures come from `npx ccusage claude session --json`, filtered to this project. The project path changed when the folder was renamed, so a unit can span two sessions (A and B). Planning tokens sit inside Unit 1's first session. Subagent usage is billed into the parent session, so Unit 3's session delta includes its workers and reviewers.

Raw token totals overstate effort: cache reads are re-reads of context already stored, and they are billed at a fraction of normal input. The cost column is ccusage's list-price estimate, not what a subscription plan actually bills.

---

## Run 2: research-repo agenda (case study)

One coordinator session cleared an agenda in a private research repo: a plan review, a results-log fix, a handoff note, restarting a long GPU job that had stopped, and refreshing a results page. Project details are left out on purpose.

**Setup:** the main session (Opus) followed the pstack Orchestrate playbook. The playbook's own size check collapsed it to a single coordinator, with no store, pilot or sub-coordinators, because one agent could finish the work. The owner asked for an Opus review before any edit. Writing went to a Sonnet worker in a git worktree. An Opus verifier, from a different model than the worker, checked the diff before it was pushed.

| Unit | Workers | Subagent tokens (agent reports) | Rework (findings fixed) | Wall time | Status |
|---|---|---|---|---|---|
| Plan review | 1 Opus reviewer, read-only | 69,658 | 8 findings: 1 blocker (the long GPU job had to start first, not third), 6 should-fix (a low runtime estimate; a result already published before it was logged; an overclaiming findings line; stale "in progress" text on the page; stale project framing in memory; a next step that was a list instead of one testable experiment), 1 nit (the exact resume command). All folded into the plan. | ~2 min | done |
| Long GPU job resume | 0, inline launch | 0 | 0. The resume skipped the finished runs as designed and appended to the output (checked on its start line). | 11 runs, ~2 h 5 min (02:57 to 05:02 UTC) | done; every pre-registered prediction held |
| Results-log fix | 1 Sonnet worker in a worktree, then 1 Opus verifier | worker 54,040; verifier 54,370 | 4. The coordinator found 1 citation that pointed at the wrong run of an earlier test. The verifier found 3 nits (a stale tense in the handoff, a counter-evidence point missing from the summary, a missing setting caveat). Verifier verdict: PASS, no blockers. | ~5 min | merged (PR 1) |
| Handoff note + memory | 0, inline | 0 | counted in the row above (1 nit) | ~3 min | merged (PR 1) |
| GPU results log | 1 Sonnet worker, then 1 Opus verifier that reran the analysis itself | worker 63,081; verifier 63,282 | 4. Verifier: 1 should-fix (a "reproduces exactly" claim that held only for the non-random runs) and 3 nits (checked a seed mean where the rule named every seed; spread given as SD instead of range; a counter-evidence point missing from the page). Verdict: PASS. | ~15 min | PR 2 open |
| Results page refresh | 0, inline (6 text edits plus 2 fix edits) | 0 | counted in the row above | ~5 min | republished twice to the same link; source added to the repo in PR 2 |
| **Run total** | 1 Opus reviewer, 2 Sonnet workers, 2 Opus verifiers | 304,431 | 16 (plan review 8, first fix 4, results log 4) | ~2 h 25 min wall time (02:40 to 05:05 UTC, mostly the GPU job) | PR 1 merged, PR 2 open |

Main-session usage (ccusage estimate, whole session, subagents included): 17,588,794 tokens, 95.4% cache reads (16,775,758), about $9.49 at list price, read at 05:05 UTC on 2026-10-23. The total also counts the session's earlier planning turns and side questions unrelated to this run, so it overstates what the run itself cost.

### Lessons

- **Review the plan before the edits.** The plan review caught an ordering mistake that would have cost about 2 hours of GPU time and a second republish of the page. It took about 2 minutes.
- **The record has to lead what gets published.** A result had reached the public summary and the page before it reached the log, and the summary claimed more than the log supported. The fix was to write the log entry first, labelled exploratory, and then soften the summary to match.
- **Collapse orchestration when the work is small.** The playbook's size check kept three units from paying for a store, a pilot and sub-coordinators.
- **Cross-model verification still finds things.** The verifier passed the work but found 3 small wording gaps that the worker and the coordinator had both missed.

### Method

The session total comes from `npx ccusage claude session --json`. Subagent figures are each agent's own token report and are already included in the session total. Wall times are approximate. The table leaves out the repo name, paths, people, session ids, experiment names, results and links.

---

## Run 3: FocusPet agenda, units 1-2 (2026-10-24)

Planner (Sonnet) ran two units. Subagent tokens are each agent's own report; planner tokens not measured.

**Setup:** My agent team (~/.claude/agents), rework cap 3. **Wall time:** ~14 min (the planner agent's reported duration, 836 s).

| Unit | Agents | Subagent tokens | Rework | Status |
|---|---|---|---|---|
| 1. #38 verify rain follow-up on main | researcher (haiku) 59,457 (no shell, inconclusive); worker (haiku) 42,917 ran verify-intent on an origin/main worktree: 110/110 | 102,374 | 0 | verified, #41 (c894000) on main; issue can close |
| 2. #34 mood digits branch | reviewer (opus) round 1 64,809 CHANGES_REQUESTED (3 findings); worker (haiku) 86,617; reviewer (opus) round 2 58,484 APPROVED | 209,910 | 1 round | approved, then merged as #46 (part of #34) |
| Overhead | researcher (haiku) plan summary 36,122 (unneeded) | 36,122 | n/a | n/a |
| **Total** | 6 agents: 2 researcher (haiku), 2 worker (haiku), 2 reviewer (opus) | 348,406 (measured) | 1 | |

Round-1 reviewer findings on unit 2 (3):

1. The backstop regex was quadratic on whitespace (34 s on 50k spaces).
2. A unit denylist dropped legitimate sentences such as "0.5 J", "0.8 kWh", "0.25 eV" and "0.5 degrees above normal".
3. Code was deleted outside closed fences (inline backticks, indented lines, an unterminated fence, a YAML line).

Assumption: backstop targets bare readbacks only.

### Lessons

- **A researcher with no shell is a wasted dispatch for anything that needs commands.** It cost 59,457 tokens and came back inconclusive.
- **Do not dispatch a summary nobody asked for.** The unneeded plan summary cost 36,122 tokens. Together these two cost 95,579 tokens, about 27% of the run's 348,406.
- **Fix:** the planner sends anything that needs commands straight to the worker.

## Run 4: FocusPet #34 follow-up, unit 1: mood-leak-v3 scorer (2026-10-24)

Planner (Sonnet) ran one unit on branch mood-scorer-34 (scorer only). Subagent tokens are each agent's own report; planner tokens not measured.

**Setup:** My agent team (~/.claude/agents), rework cap 3. **Wall time:** not measured.

| Unit | Agents | Subagent tokens | Rework | Status |
|---|---|---|---|---|
| 1. v3 scorer, tests, rescore, baseline | worker (haiku) r1 132,555; reviewer (opus) r1 70,229 CHANGES_REQUESTED (separator class missed `...`, `,`, dashes, bold); worker (haiku) r2 101,649; reviewer (opus) r2 64,185 APPROVED | 368,618 (measured) | 1 round | merged as #47 (part of #34) |

Result: selftest 54/54 (19/54 under v2); baseline 90/95, exact-numbers 0/5, controls 10/10; commits bd6b2ad, ead191a, e8af1a0, b160ca1, 530466d. Assumptions: how-are-you and are-you-ok count as state questions; any % is flagged.

**Unit 2: app-faithful seeded scenario (branch mood-scenario-34).** Planner (sonnet) subagent, then worker and reviewer (opus). Tokens: planner subagent 134,683 (measured, stopped by the owner after about 35 minutes); worker and reviewer tokens not measured. The planner never handed back a report, and the opus round-2 review was stopped by the owner after about 15 minutes because it ran too long. Round 1 review found that the first fixture copied or closely paraphrased clauses from the owner's real stored notes; that run (16/95) was withdrawn and the fixture rewritten from scratch (44/95 seeded vs 88/95 clean under v3.1). The Lead then ran an independent 3-word-run overlap check against the local data folder (0 matches, with a positive control) and squashed the history to one commit before pushing, so the withdrawn fixture never reached the remote. Status: merged as #48 (part of #34, eval only).

**Lessons (Run 4):**
- **Check eval fixtures for private data.** The first seeded fixture copied the owner's real notes. Review caught it, the run was withdrawn, and the history was squashed before pushing.
- **Time-box reviewers in the brief.** Two long reviews were stopped by the owner at about 15 minutes, which left the scenario unit without a finished round-2 review.

## Run 5: FocusPet #34 state-number rule (2026-10-24)

**Setup:** First run with the Lead setup (the main chat plans), so effectively test 7. My agent team (~/.claude/agents), rework cap 3. Subagent tokens are each agent's own report; Lead tokens not measured. Wall time: not measured.

| Unit | Agents | Subagent tokens | Rework | Status |
|---|---|---|---|---|
| Persona rule, backstop, note stripper, eval | worker (haiku) 125,044; reviewer (opus) r1 63,383 CHANGES_REQUESTED; seeded-eval worker (haiku) 55,766; worker (haiku) rework 109,687; reviewer (opus) r2 60,918 APPROVED; seeded-eval worker (haiku) 49,715 | 464,513 (measured) | 1 round | see Outcome below |

Round-1 blocker: the backstop (state words battery, status, power, charge, mood, focus) deleted ordinary replies such as "HTTP status 500." and "Your battery is at 40%." (they became the fallback "...mm."). Fix: narrowed to energy, warmth, focus and kept clauses addressed to the user. One owner call: strip percent sentences from saved notes (chosen over also dropping loose percents in replies, which would eat "Forty percent." about rain). Later reversed: write-time stripping destroyed real notes, so notes are now stored as written.

Update (same day, later rounds, measured subagent tokens): the run continued through five more review rounds, 1,128,967 measured subagent tokens in all up to round 5 (rework workers 145,092 + 131,642 + 165,566; reviewers round 3 70,756, round 4 72,460, round 5 78,938; plus the 464,513 above); the round 6 worker, its review and the page/doc work are not yet counted. Rounds: opus CHANGES_REQUESTED in rounds 1, 4 and 5 (the third hit the cap and went to the owner, who chose a narrower rule). Design changed twice on review: write-time deleting of percent sentences destroyed real notes and was removed; the read rule was narrowed twice for collisions. Main-chat (Lead) tokens not measured; wall time not measured.

### Outcome

Branch `mood-state-numbers-34`, "Part of #34", PR not yet opened (round 6 review pending). Final design: the persona rule (describe your state in words), a reply backstop limited to energy, warmth and focus, and a read-only note filter that hides a note only when a state word sits right before a number. Notes are stored as written and nothing is deleted.

Final scores (scorer v3.1, gemma3:4b, seeds 1 to 5): clean 89/95 on main to 94/95; seeded 42/95 on main to 66/95. The final read filter hides none of the eval's notes, so the seeded gain comes from the persona rule and the reply backstop. The remaining leaks are invented numbers, not copies: the stored notes prime Bit to make up a percent ("71%", "Forty-one percent"), but he does not repeat the notes' own figures. Owner hand test passed. Found, not fixed: "Set a focus session for 25 minutes." is not routed to the tool loop (#49).

### Lessons

- **Review catches what the worker's tests can't.** The first reviewer round caught a real blocker: the worker's keep list had only easy cases, and the backstop deleted ordinary replies.
- **Approvals weren't final.** Rounds 2 and 3 were APPROVED, but later owner and outside review reopened the work both times. The cap counts rejections, not rounds, so the unit ran 6 review rounds and 1.1M+ subagent tokens, about 3x Run 3. Next: also cap total rounds per unit.
- **Fuzzy text rules eat rounds.** Rounds 3 to 5 went to tuning one heuristic, telling Bit's invented numbers apart from the user's real ones. When a rule needs a third rework, stop and pick the smallest safe version.
- **No data loss beat a better score.** Stripping notes on write lifted seeded to 88/95 but destroyed real progress notes ("My draft is 50% done."). The owner chose read-only notes and a narrow read filter instead, accepting seeded at 66/95. The reviewer's data-loss rule decided the design.
- **The eval caught what unit tests didn't.** Unit checks passed throughout. The seeded scenario exposed the memory loop, and hand tests found leaks the scorer missed.
- **The rejection cap worked.** The third rejection stopped the loop and brought the decision to the owner instead of a fourth automatic round.
