# Squirrow

Local-first memory manager for AI chats (React 19 + TypeScript).

**Repo:** [thymeed/Squirrow](https://github.com/thymeed/Squirrow)

Figures are measured `subagent_tokens` unless a line says otherwise. The Lead (main chat) is not counted unless stated. Nothing here is estimated; missing figures say "not measured".

## Runs

### Run 1: Squirrow v1.1.0 (case study)

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

#### Lessons

- **Review catches real bugs, and real testing catches wrong reviews.** 22 findings were fixed before merge, but one reviewer finding (the v4-users claim in Unit 2) was wrong, and a live two-tab test disproved it.

- **Small units stay cheap.** The inline units cost $0.57 to $1.16 each. The one unit with parallel workers cost $3.42 and lost one worker to a deleted worktree.

- **Check every image before it's committed.** The first demo screenshot showed a private project and had to be replaced.

#### Method

Token figures come from `npx ccusage claude session --json`, filtered to this project. The project path changed when the folder was renamed, so a unit can span two sessions (A and B). Planning tokens sit inside Unit 1's first session. Subagent usage is billed into the parent session, so Unit 3's session delta includes its workers and reviewers.

Raw token totals overstate effort: cache reads are re-reads of context already stored, and they are billed at a fraction of normal input. The cost column is ccusage's list-price estimate, not what a subscription plan actually bills.

---

### Run 7: Squirrow five features, stacked PRs (2026-10-27 to 10-28, merged as #60, #61, #64, #65, #66)
Unit stubs, one line per merged unit, then the full entry below.
- Unit 1, #55 kind filter: PR #60, merge commit `721d499` (2026-10-28), 2 review rounds (round 1 CHANGES_REQUESTED, round 2 APPROVED), 165,725 measured subagent tokens (workers 62,990 + 46,864; reviewers 30,089 + 25,782). Lead tokens not measured.
- Unit 2, #56 backup nudge: PR #61, merge commit `dc5d485` (2026-10-28), 2 review rounds, 0 CHANGES_REQUESTED (round 1 APPROVED; after the owner's change request to count 14 days from a first-seen key, one more review APPROVED), 201,914 measured subagent tokens (workers 61,309 + 78,038; reviewers 33,604 + 28,963). Lead tokens not measured. Note: the worktree used for the rework had a node_modules junction that broke `npm test`; a real `npm ci` fixed it.
- Unit 3, #57 real token counts: PR #64, merge commit `e594549` (2026-10-28), 2 review rounds, 0 CHANGES_REQUESTED (round 1 APPROVED; the small service-worker hardening got one more APPROVED), 240,225 measured subagent tokens (workers 119,509 + 37,489; reviewers 58,173 + 25,054). Lead tokens not measured. Note: the first PR (#62) was merged into the old `feat/2-backup-nudge` branch after #61 had merged, so the code never reached `main`; the Lead opened #64 from the same commits. Cause: #62 was not retargeted to `main` after #61 merged. Follow-up issue #63 (CI check for the service worker scan regex) filed.
- Unit 4, #58 saved picks: PR #65, merge commit `e9a229f` (2026-10-28), 2 review rounds (round 1 CHANGES_REQUESTED: blocked IndexedDB upgrade could hang on "Loading..." and Load plus Auto-pick was untested; round 2 APPROVED), 400,284 measured subagent tokens (workers 138,747 + 37,126 + 107,835; reviewers 81,359 + 35,217). Lead tokens not measured. The owner chose the fix set after seeing the review (added the replaceAll rollback, kept createdAt, rejected empty names); the Lead confirmed the rollback test fails without `tx.abort()`.
- Unit 5, #59 import from chat: PR #66, merge commit `42eaf84` (2026-10-28), 3 review rounds (rounds 1 and 2 CHANGES_REQUESTED, round 3 APPROVED), 616,060 measured subagent tokens (workers 156,959 + 99,635 + 114,771 + 69,292; reviewers 71,606 + 61,829 + 41,968). Lead tokens not measured. Round 1 found a colon split that corrupted `10:30` and `C:\foo`, a dropped line above an empty fence, silent caps and two weak tests; round 2 found a regression where a huge single line imported nothing under "Nothing to import"; the owner chose to refuse such a paste rather than cut it.

#### Run 7 summary: five features shipped as #60, #61, #64, #65, #66 (issues #55 to #59, all closed)

Lead (sonnet). Stacked branches, bottom-up merges by the owner. Measured subagent tokens, models from the agent files (worker haiku, reviewer opus). Details per agent are in the Cost section of this file under "Squirrow run 2".

**What shipped:**
- #55 kind filter chips next to search; chips reset per project.
- #56 backup reminder: a banner when the last JSON export is over 14 days old, or never exported and the first-seen date (a third localStorage key, set once) is over 14 days old; Dismiss hides it for 7 days.
- #57 real o200k token counts, lazy-loaded and precached by the service worker, with the 4-character estimate as the fallback.
- #58 saved picks per project, in Export and Import JSON, with an IndexedDB v2 upgrade, a message when an older tab blocks the upgrade, and an all-or-nothing `replaceAll`.
- #59 Import from chat: headings, bullets, paragraphs and code fences split into editable draft entries with keyword-guessed kinds. No AI, no network.

**Numbers (measured subagent tokens, from the entries above and the Cost section of this file):**

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

#### Run 7 follow-ups (2026-10-28, after the five units)
- #67 overlay drag-close: PR #69, merge commit `9b34be2` (2026-10-28), 2 review rounds (round 1 CHANGES_REQUESTED: an existing saving test passed without exercising the overlay once the fix was in, mutation showed it; round 2 APPROVED), 141,444 measured subagent tokens (workers 50,186 + 38,723; reviewers 28,116 + 24,419). Lead tokens not measured. Branched from `main`, not stacked.
- #68 unwrap a fenced paste: PR #70, merge commit `576c3a9` (2026-10-28), 2 review rounds (round 1 CHANGES_REQUESTED: two required behaviors, `plaintext` staying a snippet and the indent strip, could break without any test failing, code was correct; round 2 APPROVED), 211,740 measured subagent tokens (workers 87,001 + 49,660; reviewers 47,114 + 27,965). Lead tokens not measured. Branched from `main`, rebased once onto `main` after #69 merged. The owner chose strict markdown rules for nested fences.
- #71 join hard-wrapped paragraph lines: PR #72, merge commit `8625644` (2026-10-28), 2 review rounds (round 1 CHANGES_REQUESTED: closing marks after a sentence end, italic in a label title and italic in a fence title were untested, code was correct; round 2 APPROVED), 198,197 measured subagent tokens (workers 68,704 + one stopped worker not measured; reviewers 93,002 + 36,491). Lead tokens not measured. The first worker was stopped by the Lead at the owner's request while it was still verifying; the Lead fixed one stale app test, committed the tree and sent it to the reviewer with a note that no worker report existed. The owner chose `e.g.` and `i.e.` never ending a sentence; the Lead fixed two doc comments inline after approval.
- #63 CI check that the service worker precaches every built asset: PR #73, merge commit `a28c7fa` (2026-10-28), 2 review rounds (round 1 CHANGES_REQUESTED: the check's fake cache accepted precached URLs missing from dist, so CI could pass on a build whose real install would fail; round 2 APPROVED), 95,618 measured subagent tokens (workers 35,882 + one stopped worker not measured; reviewers 32,122 + 27,614). Lead tokens not measured. The first worker was stopped by the Lead at the owner's request ("stop here for now") before it reported; on resume the Lead verified its tree (395 tests, check exits 0 on the real build, and the hash-length 8 to 9 change makes it exit 1 naming the tokenizer chunk) and sent it to the reviewer with a note that no worker report existed.

#### Run 7 follow-ups summary: #67, #68, #71 and #63 shipped as #69, #70, #72 and #73 (all issues closed)

Lead (sonnet). Measured subagent tokens, models from the agent files (worker haiku, reviewer opus). Per-agent rows are in the Cost section of this file under "Squirrow run 2 follow-ups".

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

### Run 9: Squirrow placeholder contrast and button wrap (2026-10-28)

Lead (sonnet). Project: Squirrow, Issue #74, PR #75 (CI `test` green, awaiting the owner's hand test and merge). Models from the agent files (worker haiku, reviewer opus). Per-agent rows are in the Cost section of this file under "Squirrow #74".

| Unit | Tokens | Review rounds | Result |
|---|---|---|---|
| Placeholder contrast and `.bar` button wrap (`src/ui/styles.scss`) | 93,493 (workers 39,898; reviewers 53,595) | 2 | round 1 CHANGES_REQUESTED (tablet-width overflow, owner chose to fix); round 2 APPROVED |

Lead tokens and total wall time: not measured.

Lessons:
- **The rework path ran for the first time.** The findings question, then a fresh worker with the finding verbatim, then re-review all worked as written.
- **The reviewer found what tests cannot.** jsdom does no layout, so only its headless-browser measurement caught the 721-779px overflow. A worker told to check layout said plainly that it had not.

- Run 9 unit merged (2026-10-28): PR #75 (Issue #74), placeholder contrast and button wrap. Hand tests passed; 93,493 measured subagent tokens.

#### Squirrow scalability evidence, PR 1 (2026-10-30, merged by the owner)
- Merged: PR #80 (Issue #79, part 1 of 2), merge commit 36cda89. Issue #79 stays open for part 2 (Auto-pick fix), not started.
- Units: (1) `npm run bench` core benchmark; (2) browser harness run by the Lead in Chrome, then `docs/SCALABILITY.md`, README link, `bench/browser-harness.js`.
- Agents and models: worker x3 (haiku), reviewer x4 (opus). No researcher. Browser measurements were run by the Lead directly (the worker has no browser tools).
- Review rounds: unit 1 two (round 1 CHANGES_REQUESTED, 4 findings all fixed; round 2 APPROVED). Unit 2 two (round 1 CHANGES_REQUESTED, 12 findings, owner chose all; fixed inline by the Lead at the owner's request; round 2 APPROVED). Two reviewer nits after that were fixed inline and not re-reviewed.
- Measured subagent tokens: workers 74,623 + 42,863 + 67,008 = 184,494; reviewers 44,194 + 27,627 + 35,928 + 31,075 = 138,824; total 323,318. Lead (main chat) tokens: not measured.
- Findings worth keeping: Auto-pick (`selectContext`) is O(n^2); the list is not virtualized; a 10,000-entry export is over the 10 MB import limit. Numbers are in docs/SCALABILITY.md.
- Lessons: (1) a reviewer's fix can be incomplete: the worker found the `.vite/deps_temp` leak also needed the `ssr.optimizeDeps` block on a cold cache (the reviewer could not reproduce that it was needed). (2) Reviewers caught claims the data did not support (README "under 0.4 s" while real-tokenizer Auto-pick took 6.7 s); give reviewers the raw data, not the worker's copy. (3) The auto-mode classifier blocked `git checkout -- <file>` and `gh issue create` despite the owner's approval; the owner ran the commands, one per message. (4) Three commands pasted as one line fail: git read the rest as arguments. (5) Local `main` was behind `origin/main`; fetch before branching.
- GitHub Actions was not running; format:check, test and build were run locally and stated in the PR body.

#### Squirrow scalability evidence, PR 2: incremental Auto-pick (2026-10-30, merged by the owner as PR #82 on 2026-10-31, merge commit 59c143e; CI test check passed)
- Unit: make `selectContext` incremental (Issue #79, part 2). Count per piece with the joining newline, then one exact count of the final text. Docs updated by the Lead inline (docs/SCALABILITY.md, ARCHITECTURE.md).
- Agents and models: worker x4 (haiku), reviewer x2 (opus). No researcher. Owner hand tests were run by the owner and by the Lead in Chrome (Claude in Chrome extension, local 127.0.0.1 tab only).
- Review rounds: round 1 CHANGES_REQUESTED (1 blocking: fix-up loop quadratic with the real tokenizer; 1 non-blocking test finding; owner chose both). Round 2 APPROVED with 2 optional notes (fragile SLACK = 4 in tests, doc sentence); owner chose both and the worker fixed them; no third review pass (Lead re-ran format, tests and build).
- Owner decisions: per-piece counts plus a final exact check; accept a cautious fallback estimate counter and update six tests instead of changing the algorithm.
- Measured subagent tokens: workers 67,970 + 56,800 + 51,397 + 48,376 = 224,543; reviewers 41,610 + 49,853 = 91,463; total 316,006. Lead (main chat) tokens: not measured.
- Result (bench, same machine and day): Auto-pick with the real tokenizer 33.9 ms at 1,000 entries, 182.7 ms at 5,000, 1,859.5 ms at 50,000 (was 6,700 ms at 1,000 and over 60 s at 5,000). Estimate counter 13.1 ms at 1,000 (was 385.6 ms).
- Lessons: (1) The Lead's first brief did not ask for a real-tokenizer measurement, so the blocking slowdown only showed up in review; ask for one in the first brief for any perf fix. (2) The Lead misquoted its own brief ("told not to run the tokenizer"); only the bench was banned. Quote briefs exactly. (3) A worker pass costs 48k to 68k tokens even for about 15 changed lines; the owner now lets the Lead choose inline vs worker (rule added to Lead.md). (4) Programmatic checkbox setting does not fire React's onChange; use real clicks in the browser. (5) TaskStop ended the npm wrapper but left the Vite process listening on 5173; check the port and stop the process.
- Deviations: none outside the project. Reviewer ran `tsc -b` (not requested) and deleted the tsconfig.tsbuildinfo it created; the Lead stopped a leftover Vite process on port 5173 started by the Lead.

#### Squirrow #83: click an entry's body text to tick it (2026-10-31, merged by the owner as PR #86, merge commit 1e6b8e0; CI test check passed)
- Unit: one small UI change (`src/ui/App.tsx`, `src/ui/styles.scss`, `tests/app.test.tsx`, 38 lines). Owner decided: title and body text tick, not the whole card; selecting text does not tick.
- Done inline by the Lead, no subagents (under the inline-or-worker rule in Lead.md). No reviewer pass. Checks: format:check, 406 tests and build run by the Lead; the owner-visible behavior was checked in Chrome by the Lead. Subagent tokens: none. Lead (main chat) tokens: not measured.
- Review rounds: none. Owner did not hand-test this one.
- Lessons: (1) Two of the three new tests pass on the old code because they only assert that nothing ticks; only the click-ticks test proves the feature, so say so in the PR. (2) Checking the stash-removed change against the tests (mutation check) is cheap and worth doing inline.
- Deviations: none. The Lead started and stopped its own dev server and closed its own Chrome tab.

#### Squirrow #85: raise the import size limit to 100 MB (2026-10-31, merged by the owner as PR #87, merge commit ef84369; CI test check passed)
- Unit: one constant plus tests and docs (`src/core/portability.ts`, `tests/core.test.ts`, `docs/SCALABILITY.md`, 24 insertions and 14 deletions). Owner decision (pasted from the owner and their reviewer): option A only; measure the 50,000-entry parse first; keep a limit in place; add a test; no warning, no split files. #84 was parked.
- Done inline by the Lead, no subagents (under the inline-or-worker rule in Lead.md). No reviewer pass; the owner's reviewer read the PR on GitHub. Subagent tokens: none. Lead (main chat) tokens: not measured.
- Measured (Node, raw): `parseSnapshot` alone at 50,000 entries (50,966,034 chars) 97.7, 99.3, 92.8 ms after an 83.3 ms warm-up; export plus parse median 29.8 ms at 10,000 and 140.6 ms at 50,000. Hand check in Chrome: a 13,439,624-char, 12,000-entry import succeeded, 12,000 rows survived a reload, JS heap 208 MB after import and 351 MB after reload, time "within 10.1 s" including a 5 s wait (exact import time not measured). 50,000-entry browser import not tried.
- Lessons: (1) The Lead wrote "(below)" in the PR body while placing the section above; the owner and their reviewer caught it. Reread the whole assembled PR body once before handing over the edit command. (2) Chrome's upload tool caps files at 10 MB, so a larger file was built in the page. (3) The Import JSON confirm blocks the Chrome tool; it was auto-answered in the test tab only after checking that the local dev origin held only test data. (4) The owner added a global rule: run the project's checks before any push, either the owner or Claude, and say which in the PR body (in ~/.claude/CLAUDE.md).
- Deviations: none outside the project. The Lead overrode the app's `confirm` in its own test tab (disclosed in the PR body) and deleted the test project it had created. The Lead started and stopped its own dev server and closed its own Chrome tab.

#### Squirrow #84: keep typing and ticking responsive in long lists (2026-10-31, merged by the owner as PR #88, merge commit 83e50d9; CI test check passed; #84 stays open)
- Unit: one UI change (`src/ui/App.tsx`, 64 insertions and 31 deletions): memoised `EntryRow`, stable `toggle`, list follows `useDeferredValue(query)`. Owner plan (pasted from the owner and their reviewer): profile first, then option A, measure before and after on the same bench, virtualization only if A is not enough, no paging. After the measurements the owner chose to ship A and keep #84 open.
- Done inline by the Lead, no subagents (the browser measurement loop needed the Lead). No reviewer pass; the owner's reviewer read the PR. Subagent tokens: none. Lead (main chat) tokens: not measured.
- Measured (production build, 5,000 synthetic entries, Chrome, ms): browser layout and paint 0.6 to 1.7 s in every profile; React work varied from 185 to 1,237 ms. After the change the search box updates in 95 to 436 ms (one clear step 1,531 ms) instead of 1,331 to 2,076 ms; list update 1,185 to 2,025 ms vs 1,331 to 2,076 ms before (medians 3% to 15% better, within noise). Tick total 54 to 65 ms vs 140 to 189 ms.
- Lessons: (1) A deferred list makes the stock harness measure only the search box; it showed a false win (DOM node count at the end, 34,464, equalled 3,820 rows x 9 + 84, which exposed it). Wait for the expected row count. (2) The PWA service worker can serve an old build; clear it before measuring. (3) The first profile run and the second split React vs browser time differently, so do not call one part "largest in every run" without checking each run. (4) The Lead did the review pass on the PR body against the raw numbers and fixed several overstatements (2.5x vs 2.7x, 12x vs 14x, "roughly 10%"). (5) The Lead assumed the PR was not yet created when it already was (#88); check `gh pr list` before assuming. (6) A user interruption aborted a browser call; the Lead stopped and waited.
- Deviations: none outside the project. The Lead started and stopped its own preview server (port 4173), deleted the throwaway `squirrow` database, service worker and caches it created on that address, removed its copy of the harness from `dist/`, and closed its own Chrome tab. A `content-visibility: auto` style experiment was injected in the page only and was not shipped.

## Cost

### Squirrow run 2

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

#### Squirrow run 2 follow-ups (#67 overlay drag-close, #68 fence unwrap, #71 wrapped paragraphs, #63 CI check)

| Unit | Role | Model | subagent_tokens | Verdict |
|---|---|---|---|---|
| #67 overlay drag-close, round 1 | worker | haiku | 50,186 | report delivered; Lead verified 318 tests, build, format |
| #67 overlay drag-close, round 1 | reviewer | opus | 28,116 | CHANGES_REQUESTED (existing saving test passes vacuously: click with no mousedown) |
| #67 overlay drag-close, round 2 rework (saving test, reset test) | worker | haiku | 38,723 | report delivered; Lead verified 319 tests, build, format, src unchanged |
| #67 overlay drag-close, round 2 | reviewer | opus | 24,419 | APPROVED (no blockers, no notes) |
| #68 fence unwrap, round 1 | worker | haiku | 87,001 | report delivered; Lead verified 340 tests, build, format; 3 existing tests edited (spec-driven) |
| #68 fence unwrap, round 1 | reviewer | opus | 47,114 | CHANGES_REQUESTED (plaintext not pinned; indent strip untested; both test gaps, code correct) |
| #68 fence unwrap, round 2 rework (3 tests) | worker | haiku | 49,660 | report delivered; Lead verified 351 tests, build, format, src unchanged |
| #68 fence unwrap, round 2 | reviewer | opus | 27,965 | APPROVED (non-blocking: backtick rule duplicated at two places, both now tested) |
| #71 join wrapped paragraph lines, round 1 | worker | haiku | not measured | STOPPED by the Lead mid-run at the owner's request (the owner saw it near 200k in their view, unverified; no usage figure was returned for the killed agent). Never reported; Lead verified the tree after stopping: one stale app test fixed inline, 380 tests, build, format |
| #71 join wrapped paragraph lines, round 1 | reviewer | opus | 93,002 | CHANGES_REQUESTED (5 behaviors untested: closing marks after a sentence end, italic in label title, italic in fence title; code correct) |
| #71 join wrapped paragraph lines, round 2 rework (5 tests, e.g./i.e., tidy-ups) | worker | haiku | 68,704 | report delivered; Lead verified 390 tests, build, format |
| #71 join wrapped paragraph lines, round 2 | reviewer | opus | 36,491 | APPROVED (non-blocking: two doc-comment wordings, fixed inline by the Lead; no test for I.e. alone) |
| #63 CI check of the sw scan, round 1 | worker | haiku | not measured | STOPPED by the Lead at the owner's request ("stop here for now") before it reported; no usage figure was returned for the killed agent. Work left uncommitted on branch ci/check-sw-scan, unverified. |
| #63 CI check of the sw scan, round 1 | reviewer | opus | 32,122 | CHANGES_REQUESTED (fake cache accepted URLs missing from dist; a real install would fail while the check stays green) |
| #63 CI check of the sw scan, round 2 rework (missing-URL rejection, dist message) | worker | haiku | 35,882 | report delivered; Lead verified 397 tests, build, check:sw exit 0, format |
| #63 CI check of the sw scan, round 2 | reviewer | opus | 27,614 | APPROVED (non-blocking: CLI block untested; percent-encoded names; first bad URL only) |

(#67 subtotal: workers 88,909; reviewers 52,535; total 141,444, merged as #69.)

(#68 final: workers 136,661; reviewers 75,079; total 211,740, merged as #70.)

(#71 subtotal: workers 68,704 measured + one stopped worker not measured; reviewers 129,493; total measured 198,197, merged as #72.)

(#63 final: workers 35,882 measured + one stopped worker not measured; reviewers 59,736; total measured 95,618, merged as #73.)

Squirrow run 2 final totals (measured subagent_tokens): workers (haiku) 1,460,720; reviewers (opus) 810,487; total 2,271,207 across 40 reports, 38 measured. Five units 1,624,208 (#55 to #59); follow-ups 646,999 (#67 141,444; #68 211,740; #71 198,197; #63 95,618). Not measured: the two stopped workers (the first on #71 and the first on #63) and the Lead. Wall time not measured. Merged as #60, #61, #64, #65, #66, #69, #70, #72, #73.

### Squirrow #74, 2026-10-28: placeholder contrast and button wrap

Figures are `subagent_tokens` as reported per agent. Lead tokens not measured.

| Task | Role | Model | Tokens | Tool uses | Wall time | Status |
| --- | --- | --- | --- | --- | --- | --- |
| CSS fix, round 1 | Worker | haiku | 20,188 | 10 | 52s | report delivered; Lead verified diff, format, 397 tests |
| CSS fix, round 1 | Reviewer | opus | 28,206 | 11 | 132s | CHANGES_REQUESTED (nowrap overflow at 721-779px) |
| CSS fix, round 2 | Worker | haiku | 19,710 | 5 | 30s | report delivered; Lead verified diff, format, 397 tests |
| CSS fix, round 2 | Reviewer | opus | 25,389 | 8 | 78s | APPROVED |

Total: 93,493 measured subagent tokens.

### Squirrow scalability and Auto-pick, 2026-10-30 (raw per-agent numbers)

| Task | Role | Model | subagent_tokens (tool uses, wall time) |
| --- | --- | --- | --- |
| squirrow scalability bench (unit 1) | worker | haiku | 74,623 (32 tool uses, 1029 s) |
| squirrow scalability bench (unit 1) | reviewer round 1 | opus | 44,194 (15 tool uses, 250 s) |
| squirrow scalability bench (unit 1 rework) | worker (fresh) | haiku | 42,863 (27 tool uses, 533 s) |
| squirrow scalability bench (unit 1) | reviewer round 2 | opus | 27,627 (11 tool uses, 87 s) |
| squirrow scalability docs (unit 2) | worker | haiku | 67,008 (15 tool uses, 189 s) |
| squirrow scalability docs (unit 2) | reviewer round 1 | opus | 35,928 (7 tool uses, 141 s) |
| squirrow scalability docs (unit 2) | reviewer round 2 | opus | 31,075 (7 tool uses, 96 s) |
| squirrow auto-pick incremental (PR 2, unit 1) | worker | haiku | 67,970 (28 tool uses, 319 s) |
| squirrow auto-pick incremental (PR 2, unit 1) | reviewer round 1 | opus | 41,610 (15 tool uses, 240 s) |
| squirrow auto-pick review findings 1 and 2 (unit 1 rework) | worker (fresh) | haiku | 56,800 (24 tool uses, 186 s) |
| squirrow auto-pick six tests slack (unit 1 rework) | worker (fresh) | haiku | 51,397 (17 tool uses, 185 s) |
| squirrow auto-pick incremental (PR 2, unit 1) | reviewer round 2 | opus | 49,853 (25 tool uses, 344 s) |
| squirrow auto-pick drop SLACK, doc sentence (unit 1 rework) | worker (fresh) | haiku | 48,376 (11 tool uses, 55 s) |
