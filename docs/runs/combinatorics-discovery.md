# combinatorics-discovery

A local-only research project in computational combinatorics, built with its own agent team. Results, targets and plans are left out on purpose; the process and cost data stay.

**Repo:** Local only, no remote.

Figures are measured `subagent_tokens` unless a line says otherwise. The Lead (main chat) is not counted unless stated. Nothing here is estimated; missing figures say "not measured".

## Runs

Sixteen sessions between 2026-11-05 and 2026-11-11. The Lead (main chat) is Sonnet throughout. Workers are haiku or sonnet per the agent file or the Lead's call; reviewers are opus at medium effort unless a line says otherwise. A reviewer verdict is CHANGES_REQUESTED or APPROVED (later YES or NO).

### Session 1 (2026-11-05): scaffold and toolchains

- Units: scaffold (2 review rounds), SAT toolchain (2), proof-checker toolchain (2). All approved and committed.
- Owner decision: reviewers run opus at medium effort. One worker run was stopped by the owner and relaunched.
- Incident: a worker created a multi-gigabyte toolchain folder in the home directory by running an installer without setting its home variable; the owner approved deletion.
- Paused by the owner during the next unit.

### Session 2 (2026-11-06): calibration units, validator, finding validator

- Units: three calibration units, a calibration validator, an empty ledger (inline, no review), a rework of one calibration unit, a records re-check, and a finding validator.
- Agents/models: worker haiku, reviewer opus (medium), Lead sonnet inline for small edits.
- Review rounds: calibration units 1, 2, 1; validator 3; rework 1 (plus a narrow follow-up review); records re-check 1; finding validator 3, all CHANGES_REQUESTED. Owner chose an inline fix after round 3; the Lead then self-reviewed and found and fixed one more loophole. Those last two fixes had no Opus pass.
- Finding kinds: wrong or stale wording, test gap, bypass of a check (3 rounds on the validator, each a different hole), citation loophole, a clean-looking log that hid a user-declared exception.
- Measured tokens: per-agent figures are in the Cost section; the follow-up part of the session (the units after the calibration work) totals 1,051,070, of which the source-scan unit alone is 586,355. Not measured: three reviewer figures that were not shown in hand-backs, and everything the Lead did in main chat.
- Incidents: first negative controls weakened clauses the proofs never use (no discrimination); a Lead script bug merged digits into literals and the runs were discarded; the owner allowed a one-time folder in home for an external audit, then had it deleted; three harness spill-over files outside the project were deleted with owner approval; a proof checker peaked above its memory cap and the owner raised the cap; the schema lacked a field and the owner chose to add one.
- Owner decisions: use parallel workers for large work (saved as a memory note); owner decided the rework path after the third rejection.
- Paused by the owner during a source-scan unit: rounds 1 and 2 CHANGES_REQUESTED, round 3 last; owner chose a command allow-list and two parallel workers.

### Session 3 (2026-11-07): the source-scan unit, audit wave, outside-review findings

- Units: the source-scan rework (two parallel workers, merge, three more review rounds, inline fixes after each); a research lookup; an audit wave of four small units; a batch of outside-review findings (search hardening, validator test gaps); three more batches of outside-review findings; a probes round.
- Agents/models: workers haiku or sonnet (one validator worker was haiku by agent setting and should have been sonnet), reviewer opus medium, researcher haiku, Lead sonnet inline for small edits.
- Review rounds: the source-scan unit 5 in total, all CHANGES_REQUESTED, so it was committed as a partial gate without an approval; two extra reviewer attempts were cut off by a safety classifier and gave no verdict. Audit wave: one Opus APPROVED, three units checked by the Lead plus mutants with no Opus pass; the owner asked for one reviewer instead of three and two were stopped. Search hardening: round 1 CHANGES_REQUESTED, Lead fixed, no second pass. Outside-review batch 3: round 1 CHANGES_REQUESTED, three fixes inline, no second pass, and a later outside review found holes in the same area. Probes round: part B 2 rounds (r2 APPROVED), part A 2 rounds (r2 APPROVED).
- Finding kinds: each round of the source scan found a new way through the text scan (forgery, hidden warning, build-time code, renamed statement), bypass of a check x several, test gap x several, wrong claim or docs prose x2, message wording x1. No repeated bypass kind in the probes round.
- Measured tokens: source-scan finish 659,780 in total; audit wave 516,969 (sum of the rows in the Cost section); outside-review batch 392,348; probes round 612,186. Not measured: two stopped reviewers, everything the Lead did in main chat.
- Incidents: one stray copy of a script was written to system temp and removed at once; harness spill-over files deleted with owner approval; the Lead used a haiku worker on validator code (the Lead playbook says sonnet for gates); the Lead earlier fixed review findings inline without asking or a second pass; one stale worker stopped at the owner's request.
- Owner decisions: commit the source scan as a partial gate and make a sandbox, driver, statement comparison and built-in checker replay the required scope of the next unit; close the proof-language gate with an option the owner chose; keep the probes as expected-failure tests.

### Session 4 (2026-11-07, evening, "fifth session"): second checkers and a first generator

- Units: a lane review and fix; three second checkers written from a spec in parallel, then registered; a docs-reply batch; target research; an encoder for a first re-certification target; a SAT run harness with a memory watchdog.
- Agents/models: three workers sonnet (checkers are risky code), reviewer opus medium (two rounds each), two extra opus low reviewers with a 5-minute soft budget, researchers haiku then sonnet, Lead sonnet inline for fixes. Owner asked for multiple reviewers on the next run.
- Review rounds: lane review 2 (CHANGES_REQUESTED, APPROVED); checkers 2 plus two short checks; docs-reply 1 (two YES); encoder 1 YES plus 3 rounds (NO, NO, YES after test-only fixes); harness round 1 NO.
- Finding kinds: test gap 9, bypass of a check 2 (a fail-open registry; an over-cap run accepted), spec mismatch or wrong claim 3, missing timeouts 1. Same-hole rule not triggered; the encoder tests needed 3 rounds only for wording defects.
- Measured tokens: about 1,081,000 across all blocks (471,912 + 279,912 + 61,466 + 162,230 for the encoder and harness reviews, plus reviews already counted). Not measured: the Lead's main chat, one stopped worker.
- Incidents: a haiku researcher mis-stated the target and a Sonnet verification pass caught it; the Lead's brief said malformed witness lines exit 2 (wrong; workers matched the real behaviour); the Lead wrote placeholder numbers into a review brief and a test-count claim that counted uncommitted tests, caught before the package left; a mutation run hung on a live file and left runaway processes (killed, file restored and verified); a test left a stray file in the repo root and a temp folder in .scratch (both deleted); reviewer probes did not use the memory watcher (runs were tiny); the 5-minute budget is a soft instruction only.
- Result of the first finding attempt: not proven; four capped runs timed out (a timeout is not a result); target parked. Findings in the ledger: 0.
- Owner decisions: all review findings fixed inline each round; one commit for the checkers plus registration; reviewers answer YES or NO and the Lead commits only after YES.

### Session 5 (2026-11-08, "sixth session"): the first finding

- Units: harness fixes (round 2 NO, round 3 YES, after the Lead raised the same-hole rule and the owner chose to fix the fakes first); prior-art tools; a second generator and checker; validator registration; the first finding record.
- Agents/models: workers sonnet, reviewers opus medium, prior-art haiku with a Sonnet confirmation of the negative claim, Lead sonnet inline.
- Review rounds: harness 3 total; registration 1 (YES, two test gaps closed inline afterwards, mutant-checked); first finding record 2 (r1 NO: score too high; r2 YES).
- Finding kinds: test gap 2 (fakes not writing a proof; a dead test), test side effect 1 (stray file in repo root), score above the plan rule 1, wording 3.
- Measured tokens: 171,741 (harness); 379,549 (first finding block); session subtotal 451,982 up to the generator, more in the Cost section. Not measured: the Lead's main chat, one stopped researcher, one reviewer of the checker.
- Incidents: the registration worker showed as still running after its hand-back (waiting on its own background work); stopped with TaskStop and the diff hash was checked unchanged before the commit; two scratch folders appeared during reviews and were removed.
- Owner decisions: the novelty-gate result accepted with search limits in the record; the two consistency checks done as written checks in the Opus review; owner approved appending and committing the first finding.
- Findings in the ledger: 1 (a re-certification of a published value, not new).

### Session 6 (2026-11-08, later, "seventh session"): further findings, second encoder, batch tool

- Units: two findings with a novelty gate; a batch of findings with an independently written second encoder; prior-art text updates after the owner read sources by eye; a cap raise; a batch tool.
- Agents/models: worker sonnet (second encoder), researchers sonnet (sources, licence, verification) and haiku (per-equation searches), reviewer opus medium (several rounds), Lead sonnet inline (batch runs, drafts, fixtures, licence entries).
- Review rounds: two findings 2 (NO, YES); the batch 3 (NO, NO, YES); cap raise and batch tool 3, all NO (path escape through an identifier; the same kind through symlinks, so the Lead stopped and asked; wording and test gaps), then the owner chose a structural fix and inline fixes with no round 4.
- Finding kinds: missing licence entry 1, grouped licence files 1, a rule-5 judgement for the owner 1, claim wording 4, claim without evidence 2, wrong number 1, prose 2, bypass of a check 2, robustness (tracebacks) 2, test gap 4. Same-hole rule: rounds 1 and 2 on the batch were the same kind (prior-art text claiming more than was done, written from a template); the Lead fixed with the strict option and round 3 was clean. Mutants: 12 of the Lead's own were caught.
- Measured tokens: 165,227 (two findings); 599,673 (batch plus second encoder); 195,281 (cap raise and batch tool). Not measured: the Lead's main chat.
- Incidents: the Lead's keyword scan of a source with an all-rights-reserved licence broke the spirit of the licence rule; the owner dropped it, the copy was deleted with approval and the records no longer cite it; a Sonnet researcher extracted and keyword-searched a document with no stated licence (recorded, treated as unread, harness copy deleted with approval); the Lead's first cost estimate for the cap raise understated the cost near the edge of the range and a reviewer found the real figure; the Lead read "certify it" as a bigger job than meant, started a run and stopped it when the owner said "test"; one orphaned solver was killed by PID; an overly broad pkill also killed the Lead's own shell once; the multi-agent cloud review command did not start because the repo was too large.
- Owner decisions: update the prior-art text of the earlier records (verified by the owner); test, not certify; structural fix then inline fixes with no round 4; two commits per batch.

### Session 7 (2026-11-08, "eighth session"): more findings and a re-review

- Units: another batch of findings with fixtures; known-value tests in both encoders; owner reads of sources added to the prior-art text of every earlier record; an Opus review of the earlier batch and of the latest diff.
- Agents/models: researcher haiku (equation search), researcher sonnet (verification), reviewer opus medium (the new batch 3 rounds; the earlier batch and the latest diff 3 rounds), reviewer sonnet (one diff), Lead sonnet inline (batch runs, drafts, edits).
- Review rounds: new batch 3 (NO, NO, YES); earlier batch 3 (NO, NO, YES); one diff 1 (YES). One tests-only commit and a licence rewording had no reviewer.
- Finding kinds: wording 4 (a count off by one, a sentence that overclaimed which sources the owner read, an "only one encoding" claim, a query line); no evidence defects.
- Measured tokens: 377,843. Not measured: the Lead's main chat.
- Incidents: the Lead ran a clean-archive test with a toolchain link, which started a full library build (about 27 minutes, one error, many processes); the Lead's kill command was denied, the build ended later and the count was not used; the Lead's first round-2 sentence "every source named was read by the owner" was false and the reviewer caught it; browser and computer-use tools were not available in the CLI session, so the owner read the sources by hand; the haiku report miscounted its own list by one.
- Owner decisions: items 1 and 2 of the agenda approved; second encoder on the new cells; ledger edited in place for prior-art updates; no linter; .scratch deleted; one commit for the combined text change after an unslop pass.

### Session 8 (2026-11-08, night): two small units

- Unit 1: untracked proof files; a researcher triage (66,015 tokens); the Lead compiled them in a scratch folder (clean); the owner said delete; deleted. No review needed.
- Unit 2: validator gaps by a sonnet worker, and a decode tightening by a second worker in parallel. Opus round 1 CHANGES_REQUESTED (a test gap where a mutant survived; a stale schema description), fixed inline, round 2 APPROVED. Not committed yet, waiting for the owner. Measured: unit 2 total 256,780.

### Session 9 (2026-11-09, "tenth session"): scans

- Unit: raise a size cap in both encoders. Opus round 1 CHANGES_REQUESTED (test gap x2, spec mismatch in a docstring, one design fault in a test runner), fixed inline; round 2 APPROVED.
- Lead-run scans (solver, memory cap, no subagents): scans over a larger range, with exact values reproduced and some cells left unresolved and not retried (owner decision); a second colour count scanned and bisect runs timed out (timeouts are not results).
- Incidents: the Lead used git stash on the real tree in a background command and killed a run (restored by stash pop; switched to copies under .scratch/); two harness tests failed after the cap raise, first called flaky, proven deterministic on a clean-archive comparison (cause: a very large in-process list); a variable-name clash in a scan script; a misquote from old notes, corrected.
- Owner decisions: order of work (close novelty gaps, mine resolved data, then the larger case); do not retry the unresolved cells; nothing posted or sent until a true finding exists.
- Measured tokens: not measured for this session (subagent figures were not collected). Lead main chat not measured.

### Session 10 (2026-11-10, "eleventh session"): prior art and a rule-based pass

- Lead = main chat. No code or tracked-file changes, so no worker or Opus review.
- Researcher (haiku) prior-art check: no published value found, not cleared. Researcher (sonnet) verification: confirmed through fetch summaries only; one item not re-checked.
- Bisect runs: all timeouts (not results).
- Rule-based pass: solved cells classified by rule against known coverage; some fall in published tables and the rest unmatched by any table; sources unread by agents remain a limit. No per-cell web search.
- Measured tokens: 73,237 + 48,845. Lead main chat not measured.

### Session 11 (2026-11-10, "twelfth session"): cap raise and one more finding

- Lead = main chat. Units: a reviewer-agent effort setting, a larger size cap in both encoders, one finding record.
- Agents/models: reviewer opus medium (cap raise, round 1 YES); researcher haiku (prior art); researcher sonnet (verification); reviewer sonnet (finding draft, YES; Opus not used because of the owner's usage limit). Lead sonnet inline (measurements, scans, runs, draft).
- Work: pattern mining on the solved cells with held-out tests: both guesses failed or were untestable. The previously unresolved cells were probed at the larger cap: most solved, some timed out (not results). One finding came out of the bisect, certified by a proof checker, a second encoder and a witness checker.
- Review rounds: cap raise 1 (YES, no findings); finding 1 (YES, one wording nit fixed). Finding kinds: wording 1.
- Measured tokens: 234,976. Not measured: Lead main chat.
- Incidents: a first test run of the cap raise failed two harness tests (the same cause as before: a large captured output resident in the test runner), fixed by counting lines in a child; a targeted test run errored on a wrong invocation and the commit ran anyway because of command chaining (the full suite was run on the committed tree straight after and passed); a helper script shadowed a stdlib module when run without -I; a solve wrote a large proof that was not needed (deleted); a missing decode CLI was covered by a small helper in .scratch/.
- Owner decisions: higher cap first, then the unresolved cells; a prior-art pass; certify with a proof-size check first; second encoder as well; no Opus (usage); the reviewer-agent effort line kept and committed.

### Session 12 (2026-11-10, "thirteenth session"): Opus review of the Sonnet-only work

- Lead = main chat. Unit: Opus review of the cap raise and the finding record, both earlier reviewed by Sonnet only. Fixes made in the working tree, not yet committed when this was written.
- Agents/models: reviewer opus medium (3 rounds); worker sonnet (1 pass); researcher haiku (repo lookup); researcher sonnet (extraction). Lead sonnet inline (unslop pass, wording edits, licence note).
- Review rounds: 3 (NO, NO, YES). Round 1 finding kinds: wrong claim, wrong source, licence-rule mismatch, test gap, untraceable number. Round 2: wrong claim (a "no tool read this text" statement was untrue; the Lead's own brief text assumed it before asking the owner). Round 3: YES with non-blocking notes (stale tags, an unverifiable count, reworded).
- Cause note: the round 2 finding came from the Lead offering an option that presumed a fact it had not checked. Lesson: ask the owner what reached a tool before offering wording.
- Verified by the reviewer: regenerated inputs match their recorded hashes for both encoders, witnesses pass, proofs verify under the memory watcher, the suite passes.
- Research: a pair of published conjectures is testable only at sizes already in the paper's tables, and brute-force refutation is out of reach at the current cap; candidate directions noted.
- Measured tokens: 367,386. Not measured: Lead main chat.

### Session 13 (2026-11-10, "fourteenth session"): cap checks, prior-art update, research block

- Lead = main chat. Units: input checks and a type refusal in both encoders, a cap test pinned inline (mutant-checked); a prior-art update with licence entries; a research block.
- Agents/models: reviewer opus medium (unit 1: 3 rounds NO, NO, YES; unit 2: 5 rounds NO, NO, NO, NO, YES); worker sonnet (1 pass). Lead inline for test edits, wording edits and the unslop scan.
- Finding kinds, unit 1: input handling (bool/float passed), docs (undocumented difference), test gap (a check moved after others survived). Unit 2: wrong claim x3, overstated claim (several), stale wording in a second field, missing licence entries, a query not backed by a search. Repeated kind: the Lead wrote more than the owner reported. Lesson: quote the owner's words in the draft, and send the reviewer the owner's verbatim words, not a summary.
- Owner decisions: fix all findings each round; one more round after the third NO; yes to commit both units.
- Measured tokens: 494,912. Not measured: Lead main chat.
- Research block (no tracked file changed, no review rounds): a researcher tested published conjectures against the solved cells (no counterexample); a pattern in a published table was searched by a haiku researcher and an independent sonnet verifier, which found nothing, and then the Lead's own check of the source text found it already published under a symmetry swap (wrong premise: both researchers searched only one form of the statement); a researcher looked at how a paper proves its upper bounds; one small experiment ran inline and was stopped. Measured tokens: 259,751 for the research block.

### Session 14 (2026-11-11, "fifteenth session"): one more finding, a wording sweep, a design note

- Lead = main chat. Units: known-value tests for another colour count in both encoders plus a README row; one finding record and a witness fixture; progress-page label fixes (local, untracked build script).
- Agents/models: reviewer opus medium, one resumed agent, 3 rounds: CHANGES_REQUESTED, CHANGES_REQUESTED, APPROVED. Lead inline for tests, record text and the unslop pass.
- Finding kinds: round 1 test gap, wrong source paper, a claim of a review that had not happened, missing text (certificate from one encoder only, tool hashes); round 2 one false sentence the Lead wrote. Repeated kind: the Lead wrote more than the evidence showed.
- Owner decisions: fix and re-review; yes to both commits after the third round.
- Measured tokens: 92,869 (final figure; 258,409 if the three reported figures are summed). Not measured: Lead main chat.
- Later in the session: a wording sweep across many records, edited in place as the owner had decided earlier for prior-art updates. Reviewer opus medium, 3 rounds: CHANGES_REQUESTED (one field still broad), CHANGES_REQUESTED (the Lead's new clause contradicted the record), APPROVED. Finding kinds: wrong claim twice, both written by the Lead beyond the record. Same repeated kind as above. Measured tokens: 117,383 final figure. Scratch cleanup done; two folders kept because the records and the page build still read them.
- End of session: a design note for one of the conjecture directions and a first stage as a single researcher read (sonnet). No review (docs plus research, no code). Result: the method covered only the opposite range, so the owner chose to stop before any code. Measured tokens: 74,864.

### Session 15 (2026-11-11, "sixteenth session"): research reads only

- Two researcher reads of published tables (60,092 and 53,542 tokens). No code, no review. Lead main chat not measured.

## Cost

Per-agent measured `subagent_tokens`, grouped by session. Unit names are generic. "r1", "r2" are review rounds.

### Session 1 (2026-11-05)

| Unit | Agent | Model | Tokens | Result |
|---|---|---|---|---|
| Scaffold | worker r0 | haiku | 48,942 | |
| Scaffold | reviewer r1 | opus | 50,075 | CHANGES_REQUESTED |
| Scaffold | rework worker | haiku | 63,524 | |
| Scaffold | reviewer r2 | opus | 29,581 | APPROVED |
| SAT toolchain | worker r1 | haiku | 132,202 | (interim figure not shown) |
| SAT toolchain | reviewer r1 | opus | 41,332 | |
| SAT toolchain | rework | haiku | 56,726 | |
| SAT toolchain | reviewer r2 | opus | not shown | APPROVED |
| Proof toolchain | worker | haiku | 158,160 | |
| Proof toolchain | reviewer r1 | opus | 52,532 | |
| Proof toolchain | rework worker | haiku | 114,068 | |
| Proof toolchain | reviewer r2 | opus | 56,252 | APPROVED |

One worker figure ("not reported") and one rework-worker figure ("not shown") are not measured.

### Session 2 (2026-11-06)

| Unit | Agent | Model | Tokens | Result |
|---|---|---|---|---|
| Calibration unit 1 | worker | haiku | 102,063 | |
| Calibration unit 1 | reviewer r1 | opus | 24,517 | APPROVED |
| Validator | worker r1 | haiku | 44,568 | |
| Validator | reviewer r1 | opus | 31,648 | CHANGES_REQUESTED |
| Validator | rework worker | haiku | 59,787 | |
| Validator | reviewer r2 | opus | 30,668 | CHANGES_REQUESTED |
| Validator | reviewer r3 | opus | 22,609 | APPROVED (hand-back only; figure added later) |
| Calibration unit 3 | worker | haiku | 117,272 | |
| Calibration unit 3 | reviewer r1 | opus | 43,590 | CHANGES_REQUESTED (record wording) |
| Calibration unit 3 | reviewer r2 | opus | 18,995 | APPROVED |
| Calibration unit 2 | worker | haiku | 144,438 | |
| Calibration unit 2 | reviewer r1 | opus | 35,320 | APPROVED |
| Unit 3 rework | worker | haiku | 138,822 | |
| Unit 3 rework | reviewer r1 | opus | 53,911 | APPROVED |
| Unit 3 rework | external-audit reviewer | opus | 19,011 | APPROVED |
| Records re-check | reviewer | opus | 38,949 | APPROVED; inline edits by Lead |
| Finding validator | worker r1 | haiku | 109,078 | |
| Finding validator | rework worker r2 | haiku | 113,349 | |
| Finding validator | reviewer r1 | opus | 40,783 | CHANGES_REQUESTED |
| Finding validator | round-2 worker | haiku | 121,641 | |
| Finding validator | reviewer r2 | opus | 51,401 | CHANGES_REQUESTED (a clean-looking log hid an exception) |
| Finding validator | reviewer r3 | opus | 30,426 | CHANGES_REQUESTED (an error line passed); fix inline by Lead |
| Placeholder-decider unit | worker | not stated | 56,201 | |
| Placeholder-decider unit | reviewer r1 | opus | 30,040 | APPROVED |
| Theorem-tie unit | worker | not stated | 81,609 | |
| Theorem-tie unit | reviewer r1 | opus | 34,706 | APPROVED |
| Outside-review item A | worker | not stated | 57,931 | |
| Outside-review item A | reviewer r1 | opus | 42,669 | CHANGES_REQUESTED (stale wording, fixed inline) |
| Outside-review item B | worker | not stated | 86,313 | |
| Outside-review item B | reviewer r1 | opus | 44,820 | APPROVED |
| Source-scan unit | worker r1 | not stated | 204,537 | |
| Source-scan unit | reviewer r1 | opus | 90,756 | CHANGES_REQUESTED (several bypass kinds) |
| Source-scan unit | rework worker | not stated | 226,700 | |
| Source-scan unit | reviewer r2 | opus | 64,362 | CHANGES_REQUESTED |

Session end 2026-11-06: the source-scan unit uncommitted, round 3 pending. Measured subagent_tokens for the later part of this session: 1,051,070 (source-scan unit 586,355). Lead main chat not measured.

### Session 3 (2026-11-07)

| Unit | Agent | Model | Tokens | Result |
|---|---|---|---|---|
| Source-scan rework | worker A | not stated | 120,132 | |
| Source-scan rework | worker B | not stated | 170,199 | |
| Source-scan round 3, attempt 1 | reviewer | opus | 47,341 | INCOMPLETE (cut off by a safety classifier, no verdict) |
| Source-scan round 3, attempt 2 | reviewer | opus | 38,432 | INCOMPLETE (no verdict) |
| Source-scan round 3 | reviewer | opus | 80,659 | CHANGES_REQUESTED; fixes by Lead inline |
| Source-scan round 4 | reviewer | opus | 69,032 | CHANGES_REQUESTED; fixes by Lead inline |
| Source-scan round 5 | reviewer | opus | 75,254 | CHANGES_REQUESTED; fixes by Lead inline |
| Tools lookup | researcher | haiku | 58,731 | |

Session end 2026-11-07: committed as a partial gate with owner approval, no reviewer APPROVED; measured subagent_tokens this session 659,780; Lead main chat not measured.

| Unit | Agent | Model | Tokens | Result |
|---|---|---|---|---|
| Tooling lookup (second) | researcher | haiku | 61,261 | |
| Audit wave | PASS-table worker | not stated | 88,672 | |
| Audit wave | tests worker | not stated | 129,187 | |
| Audit wave | checkers worker | not stated | 94,494 | |
| Audit wave | calibration worker | not stated | 114,041 | |
| Audit wave | citation/licence researcher | haiku | 41,926 | |
| Audit wave | checker reviewer | opus | 48,649 | APPROVED r1 |
| Audit wave | two other reviewers | opus | not measured | stopped by the owner before finishing |
| Outside-review batch | search worker | not stated | 90,268 | |
| Outside-review batch | validator test-gaps worker | not stated | 243,064 | |
| Outside-review batch | search reviewer | opus | 59,016 | CHANGES_REQUESTED r1, two fixes by Lead, no second pass |
| Outside-review batch 3 | checkers worker | haiku (agent setting) | 74,224 | |
| Outside-review batch 3 | calibration worker | haiku | 100,563 | |
| Outside-review batch 3 | schema-ties worker | haiku | 215,471 | |
| Outside-review batch 3 | reviewer | opus | 58,273 | CHANGES_REQUESTED r1 (a path bypass, a second bypass, a test gap); three fixes by Lead inline, no second pass |
| Probes: part B | witness re-run worker | haiku (agent setting; should have been sonnet) | 244,584 | |
| Probes: part B | reviewer r1 | opus | 66,068 | CHANGES_REQUESTED (two test gaps) |
| Probes: part B | fix worker | sonnet | 50,866 | hand-back said it may be interim |
| Probes: part B | reviewer r2 | opus | 48,547 | APPROVED |
| Probes: part A | gate worker | sonnet | 58,384 | |
| Probes: part A | reviewer r1 | opus | 81,629 | CHANGES_REQUESTED (message text, a test gap, plan prose) |
| Probes: part A | fix worker | sonnet | 28,320 | |
| Probes: part A | reviewer r2 | opus | 33,788 | APPROVED |

Probes round total 612,186, uncommitted at the time, owner approval pending. Lead main chat not measured.

### Session 4 (2026-11-07, "fifth session")

| Unit | Agent | Model | Tokens | Result |
|---|---|---|---|---|
| Lane review | reviewer r1 | opus | 55,748 | CHANGES_REQUESTED (a fail-open case, two test gaps) |
| Lane review | reviewer r2 | opus | 44,995 | APPROVED; fixes by Lead inline |
| Second checkers | worker 1 | sonnet | 49,409 | |
| Second checkers | worker 2 | sonnet | 45,105 | first notice said 44,083, final 45,105 |
| Second checkers | worker 3 | sonnet | 77,669 | |
| Second checkers | reviewer r1 | opus | 110,529 | CHANGES_REQUESTED (test gaps, an exit-code difference, no subprocess timeouts) |
| Second checkers | reviewer r2 | opus | 39,581 | APPROVED |
| Second checkers | 5-minute reviewer 1 | opus low | 21,457 | APPROVED |
| Second checkers | 5-minute reviewer 2 | opus low | 27,419 | APPROVED |

Session subtotal: 471,912 (lane review 100,743; checker workers 172,183; checker reviews 198,986). Fixes by Lead inline. Lead main chat not measured.

| Unit | Agent | Model | Tokens | Result |
|---|---|---|---|---|
| Target research | researcher (candidates) | haiku | 56,718 | read-only |
| Target research | researcher (claim verification) | sonnet | 26,657 | read-only |
| Target research | researcher (encoding) | sonnet | 32,135 | read-only |
| First encoder | worker | sonnet | 46,407 | uncommitted, unreviewed at the time |
| Run harness | worker | sonnet | 48,722 | uncommitted, unreviewed at the time |
| Docs-reply fix | reviewer (validator and tests) | opus | 37,873 | YES |
| Docs-reply fix | reviewer (docs accuracy) | opus low | 31,400 | YES |

Block subtotal: 279,912 (research 115,510; encoder 46,407; harness 48,722; reviews 69,273).

| Unit | Agent | Model | Tokens | Result |
|---|---|---|---|---|
| Encoder | reviewer 1 (definition equivalence) | opus | 46,233 | YES |
| Encoder | reviewer 2 (tests and mutants), rounds 1-3 | opus | 47,568 | reported 39,655, 45,491, 47,568 (figures grow, look cumulative; total taken as 47,568); NO, NO, YES after test-only fixes by Lead |
| Run harness | reviewer | opus | 68,429 | NO, round 1, interim (agent said it was waiting on its own background work) |
| Target research (end) | researcher (candidates) | sonnet | 32,588 | |
| Target research (end) | researcher (paper) | sonnet | 28,878 | |
| Harness fix worker | worker | sonnet | not measured | stopped by the Lead at the owner's request |

Block subtotal measured: 61,466 for the end-of-session research. Lead main chat not measured.

### Session 5 (2026-11-08, "sixth session")

| Unit | Agent | Model | Tokens | Result |
|---|---|---|---|---|
| Harness | reviewer r2 | opus | 73,330 | NO (reported 59,898, 60,993, 62,141, 68,707, 71,595, 73,330 across resumes, final taken) |
| Harness | fix worker | sonnet | 53,108 | |
| Harness | reviewer r3 | opus | 45,303 | YES |
| Prior-art test | researcher | haiku | 46,263 | read-only |
| Prior-art test | prior-art agent (web tools added) | haiku | 64,279 | |
| Generator | notation, licence and known-value check | sonnet | 32,944 | read-only |
| Generator | worker | sonnet | 62,632 | |
| Generator | reviewer r1 | opus | 44,355 | NO (non-ASCII digits accepted; two decode test gaps) |
| Generator | reviewer r2 | opus | 29,768 | YES |
| First finding | prior-art agent (novelty gate) | haiku | 44,792 | |
| First finding | checker worker (witness checker, strict decode, resource limits) | sonnet | 82,835 | |
| First finding | prior-art agent (gap check) | haiku | 93,262 | |
| First finding | researcher (confirmation) | sonnet | 41,702 | read-only |
| First finding | registration worker | sonnet | 89,839 | interim figure: the harness showed it still running after its hand-back; stopped by the Lead with TaskStop |
| First finding | registration reviewer | opus | 56,632 | YES |
| First finding | record reviewer r1 | opus | 46,920 | NO (score too high) |
| First finding | record reviewer r2 | opus | 51,194 | YES (may be cumulative across the resume) |
| Frontier pass | prior-art agent | haiku | 79,692 | |
| Frontier pass | verification | sonnet | 76,317 | |
| Frontier pass | hardening tests worker | sonnet | 45,910 | |
| Frontier pass | hardening review | opus | 39,108 | YES |

Subtotals: harness 171,741; generator block 136,755; first finding block 379,549 (if the second record round is cumulative, the true total is 51,194 less 46,920); frontier pass 241,027; session total up to the generator 451,982. The checker's own Opus review is not measured here. One haiku run stopped by the Lead early (not measured). Lead main chat not measured.

### Session 6 (2026-11-08, "seventh session")

| Unit | Agent | Model | Tokens | Result |
|---|---|---|---|---|
| Novelty gate | prior-art, second run | haiku | 117,936 | (first run stopped by the Lead, not measured) |
| Two findings | researcher (sources and searches) | sonnet | 45,652 | read-only |
| Two findings | reviewer r1 | opus | 69,545 | NO (licence paperwork, a rule-5 call) |
| Two findings | reviewer r2 | opus | 50,030 | YES |
| Batch | second-encoder worker | sonnet | 47,270 | |
| Batch | allowed-sources researcher | sonnet | 46,239 | |
| Batch | licence researcher | sonnet | 38,233 | |
| Batch | reviewer r1 | opus | 107,037 | NO |
| Batch | reviewer r2 | opus | 64,383 | NO |
| Batch | reviewer r3 | opus | 49,883 | YES |
| Batch | per-equation searches | haiku | 94,562 + 96,482 | |
| Batch | verification of the not-found claims | sonnet | 55,584 | |
| Cap raise and batch tool | reviewer r1 | opus | 76,963 | NO |
| Cap raise and batch tool | reviewer r2 | opus | 58,613 | NO |
| Cap raise and batch tool | reviewer r3 | opus | 59,705 | NO, then fixed inline, no round 4 |

Block subtotals: two findings 165,227; batch 599,673; cap raise and batch tool 195,281. Lead main chat not measured. The multi-agent cloud review command failed to start (repo too large), no tokens.

### Session 7 (2026-11-08, "eighth session")

| Unit | Agent | Model | Tokens | Result |
|---|---|---|---|---|
| New batch | researcher (equation search) | haiku | 77,245 | |
| New batch | researcher (verification) | sonnet | 52,030 | |
| New batch | reviewer rounds 1-3 | opus | 106,343 | reported 83,895, 99,525, 106,343 (figures grow, look cumulative; final taken); NO, NO, YES |
| One diff | reviewer | sonnet | 51,167 | YES |
| Earlier batch and latest diff | reviewer rounds 1-3 | opus | 91,058 | reported 68,839, 83,174, 91,058 (final taken); NO, NO, YES |

Block subtotal: 377,843. Lead main chat not measured.

### Session 8 (2026-11-08, night)

| Unit | Agent | Model | Tokens | Result |
|---|---|---|---|---|
| Proof files triage | researcher | not stated | 66,015 | |
| Validator gaps | worker | sonnet | 73,989 | |
| Decode tightening | worker | not stated | 70,405 | |
| Validator gaps | reviewer r1 | opus | 63,928 | CHANGES_REQUESTED |
| Validator gaps | reviewer r2 | opus | 48,458 | APPROVED |

Subagent tokens as reported; Lead tokens not measured.

### Session 9 (2026-11-09)

Opus review of a size-cap raise, rounds 1 and 2 (NO, YES): not measured. Other subagent use this session (literature lookups, prior-art passes): not measured. Lead main chat not measured. No figures estimated.

ccusage daily totals (npx ccusage@latest 20.0.48, run by the owner; Claude only, all models, whole day, main chat plus subagents, not split by agent): 2026-11-09: input 913, output 485,796, cache create 1,784,594, cache read 40,366,517, total 42,637,820 tokens, $21.13. For comparison 2026-11-08: 93,150,957 tokens, $26.40; 2026-11-07: 49,548,728 tokens, $22.30. These are ccusage figures, not subagent_tokens, so they are not added to the block subtotals.

### Sessions 10 to 15 (2026-11-10 to 2026-11-11)

| Session | Unit | Agent | Model | Tokens | Result |
|---|---|---|---|---|---|
| 10 | Prior-art check | researcher | haiku | 73,237 | |
| 10 | Verification | researcher | sonnet | 48,845 | |
| 11 | Cap raise review r1 | reviewer | opus | 51,870 | YES |
| 11 | Prior-art | researcher | haiku | 73,185 | |
| 11 | Verification of the not-found claim | researcher | sonnet | 50,288 | |
| 11 | Finding draft | reviewer | sonnet | 59,633 | YES |
| 12 | Opus review r1 | reviewer | opus | 87,753 | NO |
| 12 | Opus review r2 | reviewer | opus | 54,474 | NO |
| 12 | Opus review r3 | reviewer | opus | 67,589 | YES |
| 12 | Fixes | worker | sonnet | 54,695 | |
| 12 | Repo lookup | researcher | haiku | 44,075 | |
| 12 | Extraction | researcher | sonnet | 58,800 | |
| 13 | Cap check review r1 | reviewer | opus | 60,310 | NO |
| 13 | Fixes | worker | sonnet | 54,401 | |
| 13 | Cap check review r2 | reviewer | opus | 59,199 | NO |
| 13 | Cap check review r3 | reviewer | opus | 58,707 | YES |
| 13 | Prior-art draft r1 | reviewer | opus | 53,018 | NO |
| 13 | Prior-art draft r2 | reviewer | opus | 53,059 | NO |
| 13 | Prior-art draft r3 | reviewer | opus | 52,450 | NO |
| 13 | Prior-art draft r4 | reviewer | opus | 57,760 | NO |
| 13 | Prior-art draft r5 | reviewer | opus | 46,008 | YES |
| 13 (research) | Conjecture reader | researcher | sonnet | 70,885 | |
| 13 (research) | Pattern prior-art | researcher | haiku | 67,695 | |
| 13 (research) | Pattern verifier | researcher | sonnet | 54,439 | |
| 13 (research) | Proof-method reader | researcher | sonnet | 66,732 | |
| 14 | Finding draft r1 | reviewer | opus (medium) | 75,638 | CHANGES_REQUESTED |
| 14 | Finding draft r2 | reviewer | opus (medium) | 89,902 | CHANGES_REQUESTED |
| 14 | Finding draft r3 | reviewer | opus (medium) | 92,869 | APPROVED |
| 14 | Wording sweep r1 | reviewer | opus (medium, same resumed agent) | 107,312 | CHANGES_REQUESTED |
| 14 | Wording sweep r2 | reviewer | opus (medium, same resumed agent) | 113,301 | CHANGES_REQUESTED |
| 14 | Wording sweep r3 | reviewer | opus (medium, same resumed agent) | 117,383 | APPROVED |
| 14 | Design-note stage 0 | researcher | sonnet | 74,864 | |
| 15 | Open-table survey | researcher | not stated | 60,092 | |
| 15 | Table read | researcher | sonnet | 53,542 | |

Block subtotals: session 11 234,976; session 12 367,386; session 13 494,912 plus a research block of 259,751 (of which 193,019 is the three-agent pattern block); session 14 92,869 (258,409 if the three reported figures are summed) and 117,383 for the sweep. For resumed reviewers the figures grow across rounds and may be cumulative (the sweep continues from the earlier rounds); the final figure is taken. Lead main chat not measured. No figures estimated.
