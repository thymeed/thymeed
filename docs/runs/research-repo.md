# Research repo agenda

One coordinator session in a private research repo. Project details are left out on purpose.

**Repo:** Private repo.

Figures are measured `subagent_tokens` unless a line says otherwise. The Lead (main chat) is not counted unless stated. Nothing here is estimated; missing figures say "not measured".

## Runs

### Run 2: research-repo agenda (case study)

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

#### Lessons

- **Review the plan before the edits.** The plan review caught an ordering mistake that would have cost about 2 hours of GPU time and a second republish of the page. It took about 2 minutes.
- **The record has to lead what gets published.** A result had reached the public summary and the page before it reached the log, and the summary claimed more than the log supported. The fix was to write the log entry first, labelled exploratory, and then soften the summary to match.
- **Collapse orchestration when the work is small.** The playbook's size check kept three units from paying for a store, a pilot and sub-coordinators.
- **Cross-model verification still finds things.** The verifier passed the work but found 3 small wording gaps that the worker and the coordinator had both missed.

#### Method

The session total comes from `npx ccusage claude session --json`. Subagent figures are each agent's own token report and are already included in the session total. Wall times are approximate. The table leaves out the repo name, paths, people, session ids, experiment names, results and links.

## Cost

No separate cost rows; see the run entries above.
