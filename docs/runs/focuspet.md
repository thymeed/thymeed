# FocusPet

Desktop companion with local LLM chat (Tauri 2 + Rust, Windows).

**Repo:** Private repo.

Figures are measured `subagent_tokens` unless a line says otherwise. The Lead (main chat) is not counted unless stated. Nothing here is estimated; missing figures say "not measured".

## Runs

### Run 3: FocusPet agenda, units 1-2 (2026-10-24)

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

#### Lessons

- **A researcher with no shell is a wasted dispatch for anything that needs commands.** It cost 59,457 tokens and came back inconclusive.
- **Do not dispatch a summary nobody asked for.** The unneeded plan summary cost 36,122 tokens. Together these two cost 95,579 tokens, about 27% of the run's 348,406.
- **Fix:** the planner sends anything that needs commands straight to the worker.

### Run 4: FocusPet #34 follow-up, unit 1: mood-leak-v3 scorer (2026-10-24)

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

### Run 5: FocusPet #34 state-number rule (2026-10-24)

**Setup:** First run with the Lead setup (the main chat plans), so effectively test 7. My agent team (~/.claude/agents), rework cap 3. Subagent tokens are each agent's own report; Lead tokens not measured. Wall time: not measured.

| Unit | Agents | Subagent tokens | Rework | Status |
|---|---|---|---|---|
| Persona rule, backstop, note stripper, eval | worker (haiku) 125,044; reviewer (opus) r1 63,383 CHANGES_REQUESTED; seeded-eval worker (haiku) 55,766; worker (haiku) rework 109,687; reviewer (opus) r2 60,918 APPROVED; seeded-eval worker (haiku) 49,715 | 464,513 (measured) | 1 round | see Outcome below |

Round-1 blocker: the backstop (state words battery, status, power, charge, mood, focus) deleted ordinary replies such as "HTTP status 500." and "Your battery is at 40%." (they became the fallback "...mm."). Fix: narrowed to energy, warmth, focus and kept clauses addressed to the user. One owner call: strip percent sentences from saved notes (chosen over also dropping loose percents in replies, which would eat "Forty percent." about rain). Later reversed: write-time stripping destroyed real notes, so notes are now stored as written.

Update (same day, later rounds, measured subagent tokens): the run continued through five more review rounds, 1,128,967 measured subagent tokens in all up to round 5 (rework workers 145,092 + 131,642 + 165,566; reviewers round 3 70,756, round 4 72,460, round 5 78,938; plus the 464,513 above); the round 6 worker, its review and the page/doc work are not yet counted. Rounds: opus CHANGES_REQUESTED in rounds 1, 4 and 5 (the third hit the cap and went to the owner, who chose a narrower rule). Design changed twice on review: write-time deleting of percent sentences destroyed real notes and was removed; the read rule was narrowed twice for collisions. Main-chat (Lead) tokens not measured; wall time not measured.

#### Outcome

Branch `mood-state-numbers-34`, "Part of #34", Merged as #51 (18d5207), Oct 24, 2026, 10:11 PM ET. Final design: the persona rule (describe your state in words), a reply backstop limited to energy, warmth and focus, and a read-only note filter that hides a note only when a state word sits right before a number. Notes are stored as written and nothing is deleted.

Final scores (scorer v3.1, gemma3:4b, seeds 1 to 5): clean 89/95 on main to 94/95; seeded 42/95 on main to 66/95. The final read filter hides none of the eval's notes, so the seeded gain comes from the persona rule and the reply backstop. The remaining leaks are invented numbers, not copies: the stored notes prime Bit to make up a percent ("71%", "Forty-one percent"), but he does not repeat the notes' own figures. Owner hand test passed. Found, not fixed: "Set a focus session for 25 minutes." is not routed to the tool loop (#49).

The final review pass found the reply backstop deleting factual answers ("Solar energy is 30%."). Fixed with 11 keep cases shown red before the change. No further review round was run.

#### Lessons

- **Review catches what the worker's tests can't.** The first reviewer round caught a real blocker: the worker's keep list had only easy cases, and the backstop deleted ordinary replies.
- **Approvals weren't final.** Rounds 2 and 3 were APPROVED, but later owner and outside review reopened the work both times. The cap counts rejections, not rounds, so the unit ran 6 review rounds and 1.1M+ subagent tokens, about 3x Run 3. Next: also cap total rounds per unit.
- **Fuzzy text rules eat rounds.** Rounds 3 to 5 went to tuning one heuristic, telling Bit's invented numbers apart from the user's real ones. When a rule needs a third rework, stop and pick the smallest safe version.
- **No data loss beat a better score.** Stripping notes on write lifted seeded to 88/95 but destroyed real progress notes ("My draft is 50% done."). The owner chose read-only notes and a narrow read filter instead, accepting seeded at 66/95. The reviewer's data-loss rule decided the design.
- **The eval caught what unit tests didn't.** Unit checks passed throughout. The seeded scenario exposed the memory loop, and hand tests found leaks the scorer missed.
- **The rejection cap worked.** The third rejection stopped the loop and brought the decision to the owner instead of a fourth automatic round.

#### 2026-10-24 addendum: #34 final pass (round 6)
Lead (sonnet) ran one final unit before the PR. reviewer (opus): CHANGES_REQUESTED, 74,109 subagent tokens. worker (Haiku 5.5, worktree): fixed the backstop regex that deleted factual energy/warmth/focus answers, 109,276 subagent tokens, commit `5dcbb72`. Owner capped it at this run, so no re-review; Lead re-ran verify-persona (318/318) and the selftest instead. Total 183,385 measured subagent tokens. Push needed a force-with-lease because the branch had been rebased onto main.

Lessons (final pass, #34):
- **Probe, don't just run the suite.** All 298 gates were green while the backstop still turned "Solar energy is 30%." into "…mm.". The reviewer found it by pushing about 50 ordinary sentences through `sanitiseReply`. For text rules, put a list of ordinary sentences in the brief, not only the leaks to catch.
- **Check the review base.** Local `main` was stale, so `git diff main...HEAD` pulled in three merged PRs. The reviewer used the real base (`8b314c6`). Run `git fetch` and name the base commit in the reviewer brief.
- **A rebased branch needs a pinned force-push.** The remote held the pre-rebase copy of the same commits, so a plain push was rejected. `--force-with-lease=<branch>:<sha>` was the safe form, and only because no PR existed yet.
- **A hand test through the app can hide the raw reply.** The debug build logs only the agent loop, not plain chat. Re-sending the prompts with the eval's exported `chatRequest` showed raw against sanitised and cleared the filter. The thin replies came from routing (#49) and the persona (#29).
- **Do the log and cost entries in the same turn as the numbers.** Measured tokens (74,109 and 109,276) were written the moment both agents had reported.

### Run 6: FocusPet #49 focus-session routing (2026-10-24 to 10-25, merged as #53)
Round 1: worker (haiku) 104,517 subagent tokens, commit `debc3af` on `agent-49-focus-session`; reviewer (opus) 78,630, CHANGES_REQUESTED (3 blockers: natural phrasings stay in chat and chat can claim "session started"; new false positives auto-run `session.start`; the agent prompt never hears the timer/25-minute rules). Owner chose option A (a bare "yes" does not start a session) and added a chat-path claim backstop. Round 2 worker (haiku) dispatched on `agent-49-r2` (base `debc3af`); not yet reported. Follow-ups: #52 custom durations (filed); old unanchored `start ... session` pattern at `intent.js:114` (to file). Nothing pushed.
Round 2 worker (haiku) stopped early, nothing committed: 116,287 measured subagent tokens spent for 8 red tests in `verify-intent.mjs` §7 (unrun). Re-dispatch pending; run total so far 299,434 (104,517 + 78,630 + 116,287).

#### Run 6 continued (2026-10-25): #49 shipped as #53, plus a docs unit

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

#### Run 6 summary: #49 shipped as #53 (merged 2026-10-25, closed #49)

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

### Run 10: FocusPet issues #45, #29, #38, #52, #50 (2026-10-29, in progress, stopped at 98% usage)
- Scope (owner): #45, #29, #38, #52, #50 (#15 wardrobe art excluded). Lead = Sonnet main chat; workers and the advisor passes ran through `worker`/`reviewer` agents.
- Unit merged: PR #54 (Issue #45), sanitiseReply linear on whitespace runs. 1 review round, APPROVED; owner hand test ok.
- Unit merged: PR #56 (Issue #29), tics stripped in sanitiseReply plus she/her comment cleanup (user-facing strings were already fixed). 2 review rounds (CHANGES_REQUESTED, then APPROVED). Follow-ups filed: #55 (persona prompt still asks for stripped tics), #57 (stray `!`, trailing hm?/ah-).
- Unit merged: PR #59 (Issue #38), test-only; the routing was already fixed by #41. Issue #38 closed after the owner's hand test.
- Unit open: PR #60 (Issue #52 part 1), variable session length, honest refusal. 5 review rounds, rounds 1-4 CHANGES_REQUESTED, round 5 APPROVED (rounds 3 and 4 fixed inline by the Lead at the owner's request). Owner hand tests passed except the phrasings without the word "focus", which led to #58.
- Unit not yet PR'd: branch fix-58-session-routing, commit 4df451a (Issue #58 part 1, routing without "focus"). Reviewer stopped before reporting. Worker gates: verify-intent 255/255.
- Not started: #52 part 2 (panel picker), #50. CI is blocked by a GitHub billing message, so PR gates were run locally and posted as PR comments.
- Lessons: a worker's brief must say when a finding could be a premise problem (#38 was already fixed); resumed agents report cumulative tokens, so per-round splits are not measured; the Lead stopped at the 3rd request on #52 and the owner chose inline fixes, which worked but cost two extra review rounds.

#### Run 10 final (2026-10-29, closed by the owner)
- Merged: PR #54 (Issue #45), #56 (#29), #59 (#38, test-only; #38 closed), #60 (#52 part 1), #62 (#58 part 1, routing without the word "focus"), #64 (#52 part 2, panel picker; closes #52). #29 and #45 closed with their PRs.
- Review rounds per unit: #45 1; #29 2; #38 1; #52 part 1 5 (rounds 3 and 4 fixed inline by the Lead at the owner's request); #58 part 1 2; #52 part 2 3 (round 1 approved, rounds 2 and 3 were a CSS rule and body wording).
- Follow-ups filed: #55 (persona prompt still asks for stripped tics), #57 (stray `!`, trailing hm?/ah-), #58 (open, part 2 is the persona prompt), #63 (hours routing). Not filed: step-text contrast, quadratic MATH_PATTERNS on long numbers. Not started: #50.
- All PR gates were run locally and posted as PR comments because CI was blocked by a GitHub billing message the whole run.
- Cleanup: 37 stale local branches and 9 worktrees removed after checking each tip against its merged PR; left for the owner: .worktrees/unset (uncommitted Bit-prompt diff), .worktrees/robot (4 images), 4 local branches with no PR.
- Lessons: (1) the Lead created sibling worktrees outside the project folder against CLAUDE.md; they now go under .worktrees/. (2) A reviewer told to render the panel started preview servers and one outlived the review; later briefs forbid servers and windows during the owner's hand test. (3) `String.replace` with a replacement containing `$'` corrupted a PR body; use split/join or a function. (4) The owner's hand test caught what no gate did (the routing gap for "start a 40 minute session"); a live-run checklist belongs in the brief for any user-facing routing change. (5) #38 was already fixed; check the issue against main before briefing a worker.

### Run 12: FocusPet persona rewrite, voice eval, local arithmetic (2026-10-31 to 2026-11-01, in progress)
- Units: (1) #57, strip filler sounds at the start and end of replies, a lone leading "!" and a minus-sign guard: merged as PR #67 (CI "PR checks (dev profile)" passed in 3m23s). (2) Voice eval (15-prompt fixture, scorer with eleven flags, runner, shared state-number module, baseline run): merged as PR #68 (CI passed in 3m31s). Baseline on main, gemma3:4b, seeds 1-3, scorer voice-v4: 0 of 45 pass (leadFiller 42, trailFiller 25, noTimerMisfire 15, midFiller 6, tooLong 6). (3) New Bit SYSTEM_PROMPT (worktree .worktrees/persona-prompt, uncommitted, parked). (4) Local arithmetic parser (ui/js/calc.js, ui/js/arith.js, wiring in app.js and chat.js, tools/verify-arith.mjs, 208 checks): worker done, reviewer round 1 running.
- Agents and models: worker x6 (haiku per agent file), reviewer x9 completed plus 1 running (opus per agent file). No researcher. The Lead made several inline edits (the -.5 guard and the leading-filler rule in #57; the temperature filter, the midFiller meaning and the name exclusion in the scorer; the round-1 fixes to the prompt) and rebuilt the exe for the owner's hand tests of #57.
- Review rounds: #57 round 1 CHANGES_REQUESTED (minus sign deleted after a lead-in), round 2 APPROVED (the -.5 note was applied by the Lead afterwards), round 3 APPROVED after the owner extended the rule to leading fillers. Voice eval round 1 CHANGES_REQUESTED (6), round 2 CHANGES_REQUESTED (3), round 3 CHANGES_REQUESTED (1: "temperature" scored as a state); that was the third CHANGES_REQUESTED, so the Lead stopped and asked; the owner chose an inline fix plus one narrow round 4, which APPROVED. New prompt round 1 CHANGES_REQUESTED (invented clock time and tide, wrong sum, two unguarded rules), round 2 CHANGES_REQUESTED (the wrong sum is on the default path, because tools are off by default). Arithmetic round 1: running.
- Owner decisions: strip hm?/ah- at the start of a reply too, and ship #57 without covering mid-reply fillers; the voice (monotone/robotic, terse on lookups, warmer on wins and small talk, tide-and-reef touch, Bit has no pronouns, sentence case); the order: scorer, prompt, canned lines, delete the Marina-era code; the scorer grades the new voice (any filler fails, except one lone "mm."); midFiller counts only after the opening run; fix the timer line in its own PR; build the deterministic arithmetic parser before shipping the prompt; the local reply is just the number ("12.6."); leave the decimal display quirk for later; a fourth review round for the scorer; hand tests are the owner's; PR and commit text never says who ran what.
- Measured subagent tokens: workers 124,028 + 161,167 + 203,072 + 174,108 + 102,551 + 240,860 = 1,005,786; reviewers 59,344 + 54,942 + 95,587 + 128,129 + 112,235 + 113,341 + 95,044 + 73,349 + 101,039 = 833,010; total 1,838,796. Lead (main chat) tokens: not measured. Per-agent rows are in the Cost section of this file.
- Results: baseline above. An unrecorded 3-seed run of the new prompt: 33/45, then 37/45 after two fixes (only noTimerMisfire left, 8/45). Not saved to a results file; the recorded run waits for the arithmetic parser. Arithmetic: calc.js agrees with the compiled calc.rs on 20,028 generated expressions (the worker's differential test; not yet re-run by the reviewer).
- Lessons: (1) A passing scorer does not mean the replies are right: 39/45 hid an invented tide, an invented clock time and a wrong sum; the reviewer and a hand read caught them. The scorer should get a fabrication/correctness flag. (2) The Lead argued the calculator tool covered arithmetic without checking that tools are off by default; the reviewer read app.js:1261 and chat.js:876. Check defaults before relying on a path. (3) Prompt edits move the numbers unpredictably (the timer line went 15, 6, 12, 8 across small edits); the structural fix is a conditional rule in buildMessages. (4) An edit that removed a bullet glued the next one on and no test noticed; there is now a structure check. (5) A break-and-restore check silently did not apply twice (escaping): print "BREAK DID NOT APPLY" and check the diff before trusting a red run. (6) Briefs must forbid redirects to /tmp; two stray temp files came from that. (7) A PR body should say what was run and what it showed, not who ran it.
- Deviations: system temp. The Lead wrote /tmp/never once (an eval redirect) and deleted it at once. A worker wrote Temp\x (119 bytes, a format-check redirect); deleted after the owner said yes. A reviewer saw 3 GUID-named .tmp files appear in system temp at 23:49; nobody could show they were ours, and they were left alone. A worker's first verify-arith run hung (BroadcastChannel) and left node.exe pid 36280 running from 3:08 AM; the Lead stopped it after checking its command line. The two rea-agents node processes belong to another session and were not touched. The Lead built and launched the release exe from the persona worktree for the owner's hand tests and closed it each time.
- Not done yet: the arithmetic reviewer verdict and merge; the new prompt's recorded eval, hand test and PR; the timer-line PR; canned lines (step 3); deleting the Marina-era code (step 4); the docs PR (stale CLAUDE.md count, HANDOFF.md, .gitignore lines, "her replies").

## Cost

### FocusPet, 2026-10-22: tools, robot sprite, robot voice

| Task | Issue | Role | Model | Tokens | Tool uses | Wall time | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Calculator, weather, eval | #7 | Owner A | sonnet | 287,547 | 102 | 28m 3s | done |
| Robot sprite, drop three.js | #8 | Owner B | sonnet | 267,154 | 97 | 13m 34s | done |
| Robotic male voice | #9 | Owner C | sonnet | 129,687 | 21 | 2m 24s | done |
| Voice diff review | #9 | Reviewer | sonnet | 76,884 | 4 | 28s | done |

Finished so far: 761,272 subagent tokens.

### Notes

- All subagents run on sonnet per `pstack-models.md`, in the lean setup (no swarm lanes).

### FocusPet, 2026-10-22 (second batch): issue fixes

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

#### Advisor passes before opening PRs (opus, read-only)

| Pass | Target | Tokens | Tool uses | Result |
| --- | --- | --- | --- | --- |
| 1 | PR #23 (#18) | 85,434 | 6 | fix first (cache-key story wrong, dev cache would not save) |
| 1 | #14 branch and body | 71,702 | 4 | fix first (metadata only) |
| 1 | #11 branch and body | 74,821 | 5 | fix first (body wording) |
| 1 | #13 branch and body | 76,544 | 4 | fix first (body wording) |
| 2 | PR #23 | 78,571 | 6 | one sentence, fixed |
| 2 | bodies 14, 11, 13 | 79,117 | 6 | #13 wording, fixed |

Advisor total: 466,189 tokens. PRs opened afterwards: #23 (earlier), #24 (#14), #25 (#11), #26 (#13).

#### Session totals from `ccusage session`, read at the end of 2026-10-22

| Session | Last activity | Models | Output tokens | Cost |
| --- | --- | --- | --- | --- |
| First (fixes #19, workers) | 10:15Z | opus-5-5 $1.92, haiku-5-5 $0.24 | 66,046 | $2.16 (8,468,688 tokens) |
| Second (reviews, PRs #23-#26, issues #27-#30) | 11:32Z | sonnet-5-5 $4.70, opus-5-5 $2.82 | 125,887 | $7.51 (29,814,107 tokens) |

- The second session shows a sonnet row, which is the parent session. The first session showed none, so its $2.16 was the subagents only and the parent's turns in it are missing.
- Combined for the two sessions: about $9.67 and 38.3M tokens, mostly cache reads. Treat it as a floor for the first session.
- Only these two rows were copied; no paths or other sessions.

#### Day total from ccusage, 2026-10-22 (all projects, not FocusPet alone)

| Day | Models | Input | Output | Cache create | Cache read | Total tokens | Cost |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 2026-10-22 | haiku-5-5, opus-5-5, sonnet-5-5 | 1,180 | 418,590 | 1,937,338 | 115,888,146 | 118,245,254 | $20.55 |

- This covers every project and session that day, including the parent session and the subagents above. It cannot be split into a FocusPet or parent-session figure. For that, run `ccusage session` and copy only the totals for this session.
- Cache reads are 98% of the tokens, so the token total says little; the cost is the usable number.
#### This session, from `ccusage session` (read 2026-10-22; last activity 10:15Z, so later turns may be missing)

| Model | Input | Output | Cache create | Cache read | Cost |
| --- | --- | --- | --- | --- | --- |
| opus-5-5 | 44 | 14,063 | 275,406 | 1,293,463 | $1.92 |
| haiku-5-5 | 158 | 51,983 | 384,633 | 6,448,938 | $0.24 |
| Total | 202 | 66,046 | 660,039 | 7,742,401 | $2.16 (8,468,688 tokens) |

- ccusage lists only opus and haiku for this session. The parent ran as sonnet-5-5 and no sonnet row appears, so this is probably the subagent transcripts alone and the parent's own cost may be missing. Treat $2.16 as a floor, not the full session cost.
- Only this session's row was copied; the 69 other sessions in the output were left out.

- ccusage warned that pricing is missing for `glm-5.3-flash:cloud` and `glm-5.3:cloud`, so the cost excludes those models.

### FocusPet, 2026-10-22 (third batch): #30 + #27 PR 1, #27 PR 2, #28, #29

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

### FocusPet, 2026-10-22 (fourth batch): #38

Parent session is sonnet-5-5; all code written inline by the parent (small change), one opus advisor. Subagent tokens only; the parent is not counted.

| Task | Issue | Role | Model | Tokens | Tool uses | Wall time | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Review: rain follow-up PR (pass 1) | #38 | Advisor | opus (medium) | 80,589 | 10 | 36s | done, fix first (double assistant turns, answer not persisted, clear on failure, regex gaps); fixed inline by the parent |
| Review: rain follow-up PR (pass 2, asked by owner) | #38 | Advisor | opus (medium) | 84,300 | 8 | 47s | done, fix first (user row persisted at start); fixed inline; stale-cancel adoption hole left, predates PR |

- Not delegated on purpose: the change is 3 files, about 25 lines, so a worker's cold start would cost more than writing it. Floor figure: the parent's own tokens are not included.
- Advisor total for #38: 164,889 tokens over two passes (the second was an owner request, not the one-pass rule).
- Hand test round 1 failed (answer dropped on silent cancel); fixed inline in a fourth commit, retest passed. No third advisor pass (small change; owner asked for two).

### FocusPet, 2026-10-22 (fifth batch): #34 PR 1 (mood-leak measurement)

Parent session is sonnet-5-5; script and notes written inline by the parent (about 180 lines plus notes, narrow scope), advisors opus. Subagent tokens only; the parent is not counted, so figures are a floor.

| Task | Issue | Role | Model | Tokens | Tool uses | Wall time | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Review: mood-leak PR (pass 1) | #34 | Advisor | opus (medium) | 79,183 | 6 | 38s | done, fix first (temperature 0.2 vs app 0.7, unsanitised scoring, NAMED false positive); fixed inline, baseline re-run |
| Review: mood-leak PR (pass 2) | #34 | Advisor | opus (medium) | 82,013 | 9 | 1m 2s | done, fix first (eval posted /api/chat but the app uses /api/generate; scorer gaps); fixed inline, baseline re-run twice (53/70 both) |

- Local Ollama runs for the baselines (gemma3:4b, 42 and 70 trials) are not billed tokens.
- A scripted edit chain failed once and committed a file deletion under a misleading message before I reset it (local, never pushed). Lesson: chain edits with `&&`, not `;`.
- Advisor total for #34 PR 1: 161,196 tokens over two passes (eval PR, so two per the rule).

### FocusPet, 2026-10-22 (sixth batch): ci-cheaper

Parent session is sonnet-5-5; workflow edits written inline by the parent (one file plus stale-text fixes), advisors opus. Subagent tokens only; the parent is not counted, so figures are a floor.

| Task | Issue | Role | Model | Tokens | Tool uses | Wall time | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Review: ci-cheaper PR (pass 1) | ci-cheaper | Advisor | opus (medium) | 81,145 | 6 | 56s | done, fix first (stale README, tools/README, ci.yml comment, ooxml hint; wrong 18 min figure; no release compile in CI); fixed inline, added `cargo check --release` |
| Review: ci-cheaper PR (pass 2) | ci-cheaper | Advisor | opus (medium) | 77,685 | 5 | 38s | done, fix first (shared cache key would leave release-check artifacts unsaved; run-time figures imprecise); fixed inline with a separate job |

- Pushed #38 (`rain-followup-38`) and #34 PR 1 (`mood-digits-34`) earlier in the day; neither is merged at the time of writing.
- Advisor total for ci-cheaper: 158,830 tokens over two passes.

### FocusPet, 2026-10-22 (seventh batch): #34 PR 2 (mood digits fix)

Parent session is sonnet-5-5; code, tests and eval run inline by the parent (about 25 lines of code plus tests and notes), advisor opus. Subagent tokens only; the parent is not counted, so figures are a floor.

| Task | Issue | Role | Model | Tokens | Tool uses | Wall time | Status |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Review: mood-digits fix PR (pass 1) | #34 | Advisor | opus (medium) | 83,169 | 8 | 1m 15s | done, fix first (regex dropped legitimate sentences and code lines, quadratic on long lines, no false-positive tests); fixed inline, eval re-run |

- Merged by the owner before this batch: #38 (as #41), #34 PR 1 (#42), ci-cheaper (#43).
- Local Ollama runs (the after eval, 70 trials, run twice because the first had a dirty tree) are not billed tokens.
- Process slip: the first after-run recorded dirty=true because an uncommitted CLAUDE.md edit from the ci-cheaper branch was carried over. Committed it and re-ran.
- Advisor total for #34 PR 2: 83,169 tokens (one pass, behaviour PR). Day total of advisor passes logged above: #38 164,889; #34 PR 1 161,196; ci-cheaper 158,830; #34 PR 2 83,169 = 568,084 tokens, a floor (parent sessions not counted).
- HANDOFF.md updated on branch `handoff-update-2` (docs only, skips CI).

### Run 5: FocusPet #34 state-number rule (2026-10-24), first run with the Lead setup (main chat plans)

| Role | Model | Tokens | Result |
| --- | --- | --- | --- |
| Worker: persona rule, backstop, eval | haiku | 125,044 | done |
| Reviewer round 1 | opus | 63,383 | CHANGES_REQUESTED (blocker: false positives) |
| Worker: seeded eval, round-1 code | haiku | 55,766 | measured |
| Worker: rework + note stripper | haiku | 109,687 | done |
| Reviewer round 2 | opus | 60,918 | APPROVED |
| Worker: seeded eval, final code | haiku | 49,715 | measured |

Measured subagent total: 464,513. Lead (main chat) tokens not measured. Wall time not measured. Models are from the agent files.

#### Run 5 continued (same day), measured subagent tokens

| Role | Model | Tokens |
| --- | --- | --- |
| Worker: read-time filter + stripper fixes | haiku | 145,092 |
| Reviewer round 3 | opus | 70,756 (APPROVED) |
| Worker: subject rule | haiku | 131,642 |
| Reviewer round 4 | opus | 72,460 (CHANGES_REQUESTED) |
| Worker: read-only, number-adjacent rule | haiku | 165,566 |
| Reviewer round 5 | opus | 78,938 (CHANGES_REQUESTED, third rejection, owner chose narrower rule) |

Subtotal 664,454; with the first part of Run 5 (464,513) 1,128,967. Round 6 worker, its review and doc work not yet counted. Lead tokens not measured.

#### Outcome
PR #51, "Part of #34". Final v3.1 scores: clean 94/95, seeded 66/95. The final read filter hides none of the eval's notes, so the seeded gain comes from the persona rule and the reply backstop alone.

#### Lessons
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

### Run 10 FocusPet #45/#29/#38/#52/#58 (2026-10-29), raw per-notice numbers
Resumed agents report one number per notice and it appears cumulative (tool_uses rise); per-round splits are "not measured", so none are summed. Total: not measured.
| Unit | Agent | Model | subagent_tokens (raw notices) |
| #45 (PR 54) | worker | haiku | 99,038 |
| #45 | reviewer, resumed for the advisor pass | opus | 53,526, then 60,025 |
| #29 (PR 56) | worker r1 | haiku | 147,612 |
| #29 | worker r2 (fresh) | haiku | 164,168 |
| #29 | reviewer, resumed 3 times | opus | 87,043, then 117,063, then 127,705 |
| #38 (PR 59) | worker | haiku | 87,751 |
| #38 | reviewer (diff + body in one pass) | opus | 67,104 |
| #52 part 1 (PR 60) | worker r1, resumed once | haiku | 179,316, then 198,049 |
| #52 | worker r2 (fresh) | haiku | 151,096 |
| #52 | worker r3 (fresh) | haiku | 138,772 |
| #52 | reviewer, resumed 5 times | opus | 114,118; 153,759; 180,971; 197,599; 207,546; 215,951 |
| #58 part 1 | worker | haiku | 90,130 |
| #58 part 1 | reviewer | opus | not measured (stopped before reporting) |
Lead (main chat) tokens: not measured. #52 round 3 and 4 fixes were done by the Lead inline.

### Run 10 additions (raw per-notice numbers; cumulative-vs-segment not established, none summed)
| Unit | Agent | Model | subagent_tokens (raw notices) |
| #58 part 1 | worker | haiku | 90,130 |
| #58 part 1 | reviewer 1 (stopped at the usage limit) | opus | not measured |
| #58 part 1 | reviewer 2, resumed once | opus | 72,844, then 86,161 |
| #52 part 2 | worker, resumed once | haiku | 167,466, then 193,049 |
| #52 part 2 | reviewer, resumed 3 times | opus | 88,656; 100,962; 110,486 |
Lead (main chat) tokens: not measured. Run 10 total: not measured.

### FocusPet Run 12, 2026-10-31 to 2026-11-01: #57 filler strip, voice eval, new persona prompt, local arithmetic

Raw per-agent numbers as the completion notices reported them (the final notice is used where an agent sent an interim one). Wall time is rounded to whole seconds. One reviewer (arithmetic parser, round 1) was still running when this was written and is not included.

| Task | Role | Model | Tokens | Tool uses | Wall time | Status |
| --- | --- | --- | --- | --- | --- | --- |
| #57 filler strip (trailing + lone leading "!") | worker | haiku | 124,028 | 28 | 187 s | done, merged as #67 |
| #57 review round 1 | reviewer | opus | 59,344 | 7 | 102 s | CHANGES_REQUESTED (minus sign lost) |
| #57 review round 2 | reviewer | opus | 54,942 | 7 | 90 s | APPROVED |
| #57 review round 3 (leading hm?/ah- rule) | reviewer | opus | 95,587 | 18 | 125 s | APPROVED |
| voice eval: fixture, scorer, runner | worker | haiku | 161,167 | 26 | 287 s | done |
| voice eval review round 1 | reviewer | opus | 128,129 | 20 | 202 s | CHANGES_REQUESTED (6) |
| voice eval fix round 1 | worker (fresh) | haiku | 203,072 | 47 | 605 s | done |
| voice eval review round 2 | reviewer | opus | 112,235 | 17 | 227 s | CHANGES_REQUESTED (3) |
| voice eval fix round 2 | worker (fresh) | haiku | 174,108 | 55 | 394 s | done |
| voice eval review round 3 | reviewer | opus | 113,341 | 20 | 251 s | CHANGES_REQUESTED (1, temperature) |
| voice eval review round 4 (narrow, owner-approved) | reviewer | opus | 95,044 | 12 | 140 s | APPROVED, merged as #68 |
| new SYSTEM_PROMPT | worker | haiku | 102,551 | 32 | 173 s | done, parked |
| new SYSTEM_PROMPT review round 1 | reviewer | opus | 73,349 | 18 | 218 s | CHANGES_REQUESTED |
| new SYSTEM_PROMPT review round 2 | reviewer | opus | 101,039 | 20 | 190 s | CHANGES_REQUESTED (arithmetic) |
| local arithmetic parser | worker | haiku | 240,860 | 82 | 1072 s | done, in review |

Workers (6): 1,005,786. Reviewers (9): 833,010. Total measured subagent tokens: 1,838,796. Lead (main chat) tokens: not measured.

- Models are the model lines in ~/.claude/agents (worker haiku, reviewer opus); the notices do not report the model, so it is not independently measured.
- The arithmetic worker sent an interim notice (239,777 tokens, 934,741 ms) before its final one (240,860 tokens, 1,072,236 ms); only the final figures are counted.
