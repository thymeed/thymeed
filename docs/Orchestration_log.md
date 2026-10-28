# Orchestration log (case studies)

Smoke tests of the agent team are in [Agent_Team_Log.md](Agent_Team_Log.md). Those six tests ran the planner-subagent version, not the Lead setup (the main chat plans, following `~/.claude/orchestration/Lead.md`). The first real run with the Lead setup is therefore effectively test 7: it is the first run recorded here under that setup, and nothing before it proves it.

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

Branch `mood-state-numbers-34`, "Part of #34", Merged as #51 (18d5207), Oct 24, 2026, 10:11 PM ET. Final design: the persona rule (describe your state in words), a reply backstop limited to energy, warmth and focus, and a read-only note filter that hides a note only when a state word sits right before a number. Notes are stored as written and nothing is deleted.

Final scores (scorer v3.1, gemma3:4b, seeds 1 to 5): clean 89/95 on main to 94/95; seeded 42/95 on main to 66/95. The final read filter hides none of the eval's notes, so the seeded gain comes from the persona rule and the reply backstop. The remaining leaks are invented numbers, not copies: the stored notes prime Bit to make up a percent ("71%", "Forty-one percent"), but he does not repeat the notes' own figures. Owner hand test passed. Found, not fixed: "Set a focus session for 25 minutes." is not routed to the tool loop (#49).

The final review pass found the reply backstop deleting factual answers ("Solar energy is 30%."). Fixed with 11 keep cases shown red before the change. No further review round was run.

### Lessons

- **Review catches what the worker's tests can't.** The first reviewer round caught a real blocker: the worker's keep list had only easy cases, and the backstop deleted ordinary replies.
- **Approvals weren't final.** Rounds 2 and 3 were APPROVED, but later owner and outside review reopened the work both times. The cap counts rejections, not rounds, so the unit ran 6 review rounds and 1.1M+ subagent tokens, about 3x Run 3. Next: also cap total rounds per unit.
- **Fuzzy text rules eat rounds.** Rounds 3 to 5 went to tuning one heuristic, telling Bit's invented numbers apart from the user's real ones. When a rule needs a third rework, stop and pick the smallest safe version.
- **No data loss beat a better score.** Stripping notes on write lifted seeded to 88/95 but destroyed real progress notes ("My draft is 50% done."). The owner chose read-only notes and a narrow read filter instead, accepting seeded at 66/95. The reviewer's data-loss rule decided the design.
- **The eval caught what unit tests didn't.** Unit checks passed throughout. The seeded scenario exposed the memory loop, and hand tests found leaks the scorer missed.
- **The rejection cap worked.** The third rejection stopped the loop and brought the decision to the owner instead of a fourth automatic round.

### 2026-10-24 addendum: #34 final pass (round 6)
Lead (sonnet) ran one final unit before the PR. reviewer (opus): CHANGES_REQUESTED, 74,109 subagent tokens. worker (Haiku 5.5, worktree): fixed the backstop regex that deleted factual energy/warmth/focus answers, 109,276 subagent tokens, commit `5dcbb72`. Owner capped it at this run, so no re-review; Lead re-ran verify-persona (318/318) and the selftest instead. Total 183,385 measured subagent tokens. Push needed a force-with-lease because the branch had been rebased onto main.

Lessons (final pass, #34):
- **Probe, don't just run the suite.** All 298 gates were green while the backstop still turned "Solar energy is 30%." into "…mm.". The reviewer found it by pushing about 50 ordinary sentences through `sanitiseReply`. For text rules, put a list of ordinary sentences in the brief, not only the leaks to catch.
- **Check the review base.** Local `main` was stale, so `git diff main...HEAD` pulled in three merged PRs. The reviewer used the real base (`8b314c6`). Run `git fetch` and name the base commit in the reviewer brief.
- **A rebased branch needs a pinned force-push.** The remote held the pre-rebase copy of the same commits, so a plain push was rejected. `--force-with-lease=<branch>:<sha>` was the safe form, and only because no PR existed yet.
- **A hand test through the app can hide the raw reply.** The debug build logs only the agent loop, not plain chat. Re-sending the prompts with the eval's exported `chatRequest` showed raw against sanitised and cleared the filter. The thin replies came from routing (#49) and the persona (#29).
- **Do the log and cost entries in the same turn as the numbers.** Measured tokens (74,109 and 109,276) were written the moment both agents had reported.

## Run 6: FocusPet #49 focus-session routing (2026-10-24 to 10-25, merged as #53)
Round 1: worker (haiku) 104,517 subagent tokens, commit `debc3af` on `agent-49-focus-session`; reviewer (opus) 78,630, CHANGES_REQUESTED (3 blockers: natural phrasings stay in chat and chat can claim "session started"; new false positives auto-run `session.start`; the agent prompt never hears the timer/25-minute rules). Owner chose option A (a bare "yes" does not start a session) and added a chat-path claim backstop. Round 2 worker (haiku) dispatched on `agent-49-r2` (base `debc3af`); not yet reported. Follow-ups: #52 custom durations (filed); old unanchored `start ... session` pattern at `intent.js:114` (to file). Nothing pushed.
Round 2 worker (haiku) stopped early, nothing committed: 116,287 measured subagent tokens spent for 8 red tests in `verify-intent.mjs` §7 (unrun). Re-dispatch pending; run total so far 299,434 (104,517 + 78,630 + 116,287).

### Run 6 continued (2026-10-25): #49 shipped as #53, plus a docs unit

Lead (sonnet). Measured subagent tokens, models from the agent files (worker haiku, reviewer opus):

| Unit | Agent | Tokens | Verdict / result |
|---|---|---|---|
| #49 review (debc3af) | reviewer | 96,336 | CHANGES_REQUESTED (prompt never taught session.start; "yes please" claim 5/5; routing tails too loose) |
| Rework 1 | worker | 96,441 | `2df5feb` (committed by Lead; the worker's shell was blocked) |
| #49 review | reviewer | 75,331 | CHANGES_REQUESTED (bare "yes"/"do it" still claimed 6/20 and 15/20) |
| Rework 2: sanitiseReply claim filter | worker | 120,632 | `6838bcc` |
| #49 review | reviewer | 94,328 | CHANGES_REQUESTED, third rejection (bare-verb claim "commencing." survived 3/150) |
| Lead inline fix, owner's choice | Lead | not measured | `cbed0e5` |
| Bubble shows sanitised reply | worker | 80,239 | `1d111b7` |
| Review of cbed0e5 + 1d111b7 | reviewer | 91,152 | APPROVED |
| Docs refresh | worker | 71,302 | `3784659` |
| Docs review | reviewer | 89,963 | CHANGES_REQUESTED, then APPROVED after a rework (see note) |
| Docs rework (PR merged, red main CI) | worker | 72,015 | `a0b7452` |

Total this session 887,739 measured subagent tokens. The docs reviewer was resumed for round 2; its two reports were 77,458 and 89,963 and the second looks cumulative (4 more tool calls in 49 s), so I count 89,963 once. If it is per-run, the total is 965,197. Run 6 across both sessions: 1,187,173 (or 1,264,631 on that reading). Lead tokens and wall time not measured. Two worker/reviewer shell calls were blocked by an auto-mode classifier outage; no tokens are known for those.

Outcome: #53 merged as `1ae5b1d` (2026-10-25 07:52 UTC), closing #49. Final gates: verify-persona 369/369, verify-intent 151/151, verify-stream 23/23, cargo test --release 150. Live probes: session.start called 30/30 (was 1/5, 1/5, 0/5). `main` CI went red on the push run from a flaky timing-ratio assertion in verify-persona (`dropPercentSentences scales linearly`, 3.70 vs limit 3.5); the PR run passed it at 2.21. A re-run was started by someone else and was still running at the time of writing. Docs branch `handoff-update-3` approved on its second review, not pushed. Owner hand tests the final build on 2026-10-26.

Lessons (run 6, second session):
- **Hand test beat every probe and reviewer.** The pet bubble streamed the raw model text and Save wrote it to disk; `sanitiseReply` only fed history and voice. Three reviewer rounds and 150 simulated conversations called `sanitiseReply` directly, so none saw it. Brief reviewers on the path the user sees, not the function.
- **The owner's choice at the cap worked, but needed a review after.** The third rejection went to the owner, who chose an inline fix. It then got its own review with the next unit, and that review passed.
- **Check the PR state before committing a status page.** The docs commit went stale 12 seconds later when #53 merged; the reviewer caught it and it cost a second docs round.
- **Worktrees share one target dir.** The owner's `cargo build` in the main checkout overwrote the exe built from the branch, so a lag comparison ran `main`, not the branch. Now a trap in CLAUDE.md.
- **Timing ratio assertions flake on shared runners.** A ratio of two ~10 ms measurements failed once on CI. Fix is a median of several runs or a larger input.
- **The classifier outage looked like a refusal.** Eight "no verdict" errors in a row blocked commits and agent launches; switching to manual approval worked. Do not loop on it.

### Run 6 summary: #49 shipped as #53 (merged 2026-10-25, closed #49)

**What #53 changed:**
- Anchored session-routing patterns in `intent.js`, so focus-session requests reach the tool loop.
- `session.start` always reports 25 minutes, and `agent.rs` prompt rules teach the model that.
- Timer requests get the exact-words hint.
- A plain-chat `sanitiseReply` filter replaces fake session claims, and the bubble shows the sanitised reply.
- Custom durations were split out to #52.

**Numbers (measured subagent tokens, from the entries above):**

| Part | Tokens | Review result |
|---|---|---|
| #49 code unit (workers 5 + reviewers 5, both sessions) | 953,893 | 4 CHANGES_REQUESTED (78,630; 96,336; 75,331; 94,328), then APPROVED (91,152). The third rejection went to the owner, who chose an inline fix. |
| Docs unit (`handoff-update-3`) | 233,280 | 1 CHANGES_REQUESTED, then APPROVED after a rework. |
| **Run 6 total** | **1,187,173** (1,264,631 if the resumed docs reviewer's two reports are per-run) | |

- **Review rounds:** 5 on the code unit, 2 on the docs unit.
- **Lead tokens:** not measured.
- **Wall time:** not measured. Only dates are recorded (2026-10-24 to 10-25).

## Run 7: Squirrow five features, stacked PRs (2026-10-27 to 10-28, merged as #60, #61, #64, #65, #66)
Unit stubs, one line per merged unit, then the full entry below.
- Unit 1, #55 kind filter: PR #60, merge commit `721d499` (2026-10-28), 2 review rounds (round 1 CHANGES_REQUESTED, round 2 APPROVED), 165,725 measured subagent tokens (workers 62,990 + 46,864; reviewers 30,089 + 25,782). Lead tokens not measured.
- Unit 2, #56 backup nudge: PR #61, merge commit `dc5d485` (2026-10-28), 2 review rounds, 0 CHANGES_REQUESTED (round 1 APPROVED; after the owner's change request to count 14 days from a first-seen key, one more review APPROVED), 201,914 measured subagent tokens (workers 61,309 + 78,038; reviewers 33,604 + 28,963). Lead tokens not measured. Note: the worktree used for the rework had a node_modules junction that broke `npm test`; a real `npm ci` fixed it.
- Unit 3, #57 real token counts: PR #64, merge commit `e594549` (2026-10-28), 2 review rounds, 0 CHANGES_REQUESTED (round 1 APPROVED; the small service-worker hardening got one more APPROVED), 240,225 measured subagent tokens (workers 119,509 + 37,489; reviewers 58,173 + 25,054). Lead tokens not measured. Note: the first PR (#62) was merged into the old `feat/2-backup-nudge` branch after #61 had merged, so the code never reached `main`; the Lead opened #64 from the same commits. Cause: #62 was not retargeted to `main` after #61 merged. Follow-up issue #63 (CI check for the service worker scan regex) filed.
- Unit 4, #58 saved picks: PR #65, merge commit `e9a229f` (2026-10-28), 2 review rounds (round 1 CHANGES_REQUESTED: blocked IndexedDB upgrade could hang on "Loading..." and Load plus Auto-pick was untested; round 2 APPROVED), 400,284 measured subagent tokens (workers 138,747 + 37,126 + 107,835; reviewers 81,359 + 35,217). Lead tokens not measured. The owner chose the fix set after seeing the review (added the replaceAll rollback, kept createdAt, rejected empty names); the Lead confirmed the rollback test fails without `tx.abort()`.
- Unit 5, #59 import from chat: PR #66, merge commit `42eaf84` (2026-10-28), 3 review rounds (rounds 1 and 2 CHANGES_REQUESTED, round 3 APPROVED), 616,060 measured subagent tokens (workers 156,959 + 99,635 + 114,771 + 69,292; reviewers 71,606 + 61,829 + 41,968). Lead tokens not measured. Round 1 found a colon split that corrupted `10:30` and `C:\foo`, a dropped line above an empty fence, silent caps and two weak tests; round 2 found a regression where a huge single line imported nothing under "Nothing to import"; the owner chose to refuse such a paste rather than cut it.

### Run 7 summary: five features shipped as #60, #61, #64, #65, #66 (issues #55 to #59, all closed)

Lead (sonnet). Stacked branches, bottom-up merges by the owner. Measured subagent tokens, models from the agent files (worker haiku, reviewer opus). Details per agent are in `~/.claude/cost.md` under "Squirrow run 2".

**What shipped:**
- #55 kind filter chips next to search; chips reset per project.
- #56 backup reminder: a banner when the last JSON export is over 14 days old, or never exported and the first-seen date (a third localStorage key, set once) is over 14 days old; Dismiss hides it for 7 days.
- #57 real o200k token counts, lazy-loaded and precached by the service worker, with the 4-character estimate as the fallback.
- #58 saved picks per project, in Export and Import JSON, with an IndexedDB v2 upgrade, a message when an older tab blocks the upgrade, and an all-or-nothing `replaceAll`.
- #59 Import from chat: headings, bullets, paragraphs and code fences split into editable draft entries with keyword-guessed kinds. No AI, no network.

**Numbers (measured subagent tokens, from the entries above and `~/.claude/cost.md`):**

| Unit | PR | Tokens | Review rounds | Result |
|---|---|---|---|---|
| 1 kind filter | #60 | 165,725 | 2 | 1 CHANGES_REQUESTED, then APPROVED |
| 2 backup nudge | #61 | 201,914 | 2 | 0 rejections; the owner's change request got its own review |
| 3 real token counts | #64 | 240,225 | 2 | 0 rejections; one service-worker hardening pass after approval |
| 4 saved picks | #65 | 400,284 | 2 | 1 CHANGES_REQUESTED, then APPROVED |
| 5 import from chat | #66 | 616,060 | 3 | 2 CHANGES_REQUESTED, then APPROVED |
| **Total** | | **1,624,208** | 11 | workers 1,130,564 (haiku); reviewers 493,644 (opus) |

24 subagent reports in all. Lead tokens: not measured. Wall time: not measured (the work ran 2026-10-27 to 10-28).

**Open follow-ups and unchecked items:**
- #63 CI check that every built `.js` chunk matches the service worker scan pattern (the 8-character hash in the pattern is Vite's default). Since done: merged as #73, see the follow-ups below.
- Not checked in a real browser: the offline tokenizer cache and the token caption; the "newer version takes over" and "connection lost" messages (the owner confirmed only the blocked-tab message, with two tabs); the Import from chat dialog. Since then, the token caption, the tokenizer's presence in the cache and the Import from chat dialog have been checked (see the follow-ups below); a true offline reload and the two database messages are still untested.
- Accepted risks are listed in each PR body (for example clock-skew on the reminder, Auto-pick speed near 500 entries, heuristic misses in the chat import).

Lessons:
- **Retarget the next PR right after a merge.** #62 was based on `feat/2-backup-nudge` and was merged into that branch after #61 had gone to `main`, so unit 3 never reached `main` until the Lead opened #64 from the same commits. The owner's stack rule already said to retarget; the step was skipped. Do it, and check the base, before telling the owner a PR is ready to merge.
- **Check the test environment before trusting a worker's "verified".** The rework worktree used a `node_modules` junction and `npm test` failed in it; the worker verified with a temporary config. The Lead replaced the junction with a real `npm ci` and re-ran the real suite. A temp config also appeared in the repo once; the owner asked for it to be removed and it never entered a commit (it is now in the local exclude list).
- **Reviewers found real defects that green tests missed.** An empty-project screen hidden by a filter chip (#55), an IndexedDB upgrade that could leave the app on "Loading..." (#58), a failed import that could leave the data cleared (#58), a colon split that corrupted times and paths and a dropped line above an empty fence (#59). Mutation checks also showed tests that passed under the bug they were named for (the Enter test, Back, Load plus Auto-pick); each was fixed and re-proved.
- **The Lead re-ran claims the worker skipped.** The worker never ran the `replaceAll` rollback test against the old code; the Lead removed `tx.abort()` and confirmed it fails. Self-reports were re-checked against git and real test output before each review.
- **The owner's product calls shaped the code more than the first draft did.** Counting the reminder from a first-seen key instead of `updatedAt` closed a gap where someone who keeps editing a few entries would never see it; refusing a giant single line instead of cutting it; tokenizer size accepted at about 1 MB gzipped. Asking those questions before the worker started, not after its first draft, would have saved a pass on #59, where the first pass came back with four product questions.
- **The rejection cap worked, and unit 5 still cost the most.** Two rejections, 616,060 tokens, about 38% of the run. The fuzzy text heuristics and a UI dialog gave many places to find defects, and each fix opened a neighbouring one (the cap fix caused the huge-line regression). Next time, brief heuristic parsers with a property check (no input text lost) and the cap behavior on the first pass.
- **Show the owner the review before rework.** The owner asked to see reviewer results first and to be asked through the question tool; both were done from then on, and each choice (which findings to fix, which extras) was theirs.
- **Small inline test additions by the Lead were cheap and fine** (two tests in #55 and #61, plus rebases and one conflict resolution); production code went through workers.

### Run 7 follow-ups (2026-10-28, after the five units)
- #67 overlay drag-close: PR #69, merge commit `9b34be2` (2026-10-28), 2 review rounds (round 1 CHANGES_REQUESTED: an existing saving test passed without exercising the overlay once the fix was in, mutation showed it; round 2 APPROVED), 141,444 measured subagent tokens (workers 50,186 + 38,723; reviewers 28,116 + 24,419). Lead tokens not measured. Branched from `main`, not stacked.
- #68 unwrap a fenced paste: PR #70, merge commit `576c3a9` (2026-10-28), 2 review rounds (round 1 CHANGES_REQUESTED: two required behaviors, `plaintext` staying a snippet and the indent strip, could break without any test failing, code was correct; round 2 APPROVED), 211,740 measured subagent tokens (workers 87,001 + 49,660; reviewers 47,114 + 27,965). Lead tokens not measured. Branched from `main`, rebased once onto `main` after #69 merged. The owner chose strict markdown rules for nested fences.
- #71 join hard-wrapped paragraph lines: PR #72, merge commit `8625644` (2026-10-28), 2 review rounds (round 1 CHANGES_REQUESTED: closing marks after a sentence end, italic in a label title and italic in a fence title were untested, code was correct; round 2 APPROVED), 198,197 measured subagent tokens (workers 68,704 + one stopped worker not measured; reviewers 93,002 + 36,491). Lead tokens not measured. The first worker was stopped by the Lead at the owner's request while it was still verifying; the Lead fixed one stale app test, committed the tree and sent it to the reviewer with a note that no worker report existed. The owner chose `e.g.` and `i.e.` never ending a sentence; the Lead fixed two doc comments inline after approval.
- #63 CI check that the service worker precaches every built asset: PR #73, merge commit `a28c7fa` (2026-10-28), 2 review rounds (round 1 CHANGES_REQUESTED: the check's fake cache accepted precached URLs missing from dist, so CI could pass on a build whose real install would fail; round 2 APPROVED), 95,618 measured subagent tokens (workers 35,882 + one stopped worker not measured; reviewers 32,122 + 27,614). Lead tokens not measured. The first worker was stopped by the Lead at the owner's request ("stop here for now") before it reported; on resume the Lead verified its tree (395 tests, check exits 0 on the real build, and the hash-length 8 to 9 change makes it exit 1 naming the tokenizer chunk) and sent it to the reviewer with a note that no worker report existed.

### Run 7 follow-ups summary: #67, #68, #71 and #63 shipped as #69, #70, #72 and #73 (all issues closed)

Lead (sonnet). Measured subagent tokens, models from the agent files (worker haiku, reviewer opus). Per-agent rows are in `~/.claude/cost.md` under "Squirrow run 2 follow-ups".

| Unit | PR | Tokens | Review rounds | Result |
|---|---|---|---|---|
| #67 dialogs close on a selection drag | #69 | 141,444 | 2 | 1 CHANGES_REQUESTED (a test passed vacuously), then APPROVED |
| #68 unwrap a one-fence paste | #70 | 211,740 | 2 | 1 CHANGES_REQUESTED (two behaviors untested), then APPROVED |
| #71 join hard-wrapped paragraph lines | #72 | 198,197 | 2 | 1 CHANGES_REQUESTED (three behaviors untested), then APPROVED; first worker stopped, not measured |
| #63 CI check of the service worker scan | #73 | 95,618 | 2 | 1 CHANGES_REQUESTED (the fake accepted missing URLs), then APPROVED; first worker stopped, not measured |
| **Follow-ups total** | | **646,999** | 8 | workers 330,156 (haiku); reviewers 316,843 (opus) |

The whole run, five units plus follow-ups: 2,271,207 measured subagent tokens (1,624,208 + 646,999) across 40 reports, 38 of them measured. The two stopped workers (the first on #71 and the first on #63) returned no token figure and are marked "not measured". Lead tokens and wall time: not measured.

**Housekeeping done (2026-10-28):** the scratch worktree was removed, and the eight merged local branches from the five units and the first three follow-ups were deleted with the safe delete after checking each was merged into `main`. GitHub had already deleted the remote branches when the PRs merged. Branches that were not part of this run were left alone (`docs/auto-pick`, `docs/claude-status-1.1.0`, `feat/stamp-sw-version`, `release/1.1.0`, four `worktree-agent-*`).

**Still open (not code):**
- Hand tests passed: the owner passed steps 0 to 4 and the draft list of step 5 on the live site, and a separate Claude session with the Claude in Chrome extension passed all seven steps against a local `npm run preview` copy, as reported by the owner (chips and shortcuts, token note and tokenizer cache, saved picks, Import from chat including Enter and Save, a wrapped paragraph and a markdown fence, the backup banner, and a dialog drag onto the backdrop). Not yet tested: a true offline reload, dark mode, phone width, and a backdrop tap on a phone.
- Not tested anywhere: the "newer version takes over" and "connection lost" database messages, Safari and Firefox, and the native prompt and confirm dialogs (new project, deletes), which the Chrome run had to stub.
- Accepted risks are listed in each PR body.

Lessons:
- **A fix can make an existing test pass for the wrong reason.** In #67 the saving-guard test called `.click()` with no mousedown, so once the overlay needed a mousedown first it passed without exercising the guard. Only a mutation (remove the guard, test still green) showed it. When a change alters how an existing handler is reached, re-prove the existing tests that touch it.
- **Test the fake as well as the code.** The #63 check ran the real service worker against a fake cache that accepted any URL, so it could pass while a real install would fail. Reviewing what a fake leaves out found it. Brief reviewers to ask what the fake does not model.
- **Unreported worker work still has to be checked from scratch.** Both stopped workers left usable trees, but nothing they said could be trusted. The Lead ran the gates and the key demonstrations itself, fixed one stale test inline, and told the reviewer there was no worker report. That worked, and cost little.
- **Stopping a worker returns no token count.** A budget and stop rule in the brief ("do only these items, then stop and report") kept later workers short; the stop request that ended the #63 worker came from the owner's usage limit, not a loop.
- **Derive expected results by running the code.** The hand-test sample paste in the guide gave two wrong expectations (a `## Files` section hint turns "Node.js" into a file note, and a colon after a digit is not a label). The app was right. Run the parser on a sample before handing it to the owner.
- **Test gaps were the usual finding in the follow-ups.** Three of the four follow-ups were rejected once for behaviors that no test would have caught breaking. Asking the worker up front for a mutation proof per new test, as the briefs did from #68 on, made the second round short.
- **Owner-supplied issue text works as the brief.** #67, #68 and #71 arrived as pasted text; filing each exactly as written, then briefing the worker with the text verbatim plus the gaps the Lead filled (sentence rules, nested bullets), kept the owner's wording as the contract.
- **Small inline edits by the Lead stayed cheap and safe:** one stale test expectation, two doc comments, and rebases; production code still went through workers.
