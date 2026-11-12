# agent-loop

Lead, worker, check and reviewer loop on local Ollama models (Python + LangGraph).

**Repo:** Private repo.

Figures are measured `subagent_tokens` unless a line says otherwise. The Lead (main chat) is not counted unless stated. Nothing here is estimated; missing figures say "not measured".

## Runs

### Run 11: agent-loop sandbox hardening and csvrow variants (2026-10-30, paused by the owner)
- Scope: finish the reserved-requirements plan (`proud-rolling-hartmanis`), fix the temp leak, then build the csvrow experiment variants. Lead = Sonnet main chat; `worker` (haiku), `reviewer` (opus), `researcher` (haiku).
- Units approved: 1, 2, 2b (reserved names, constraints file, pytest-set verifier) in 1 round; unit 3 (build context under .scratch/) in 2 rounds (round 1 found a real bug in unit 2b: any requirement with a top-level module, e.g. `six`, failed the build); `prompt_variants` for csvrow in 2 rounds (round 1: default prompts not pinned exactly, control could enable a variant, console line untested). Owner committed the first four; the prompt_variants unit is approved and uncommitted.
- Design change before any run: `plan-only` variant replaced by `no-plan` (the lead already forbade code in the plan). Owner decided.
- Measured subagent tokens this session: 671,080 across 13 agent runs (rows in the Cost section of this file). Lead tokens: not measured. Earlier-session rows for this plan are in the Cost section of this file and not in this total.
- Lessons: (1) Approving a build safeguard on its own tests missed a bug that a real build with an ordinary requirement exposed; briefs now require one. (2) Worker briefs that say "count system temp" must also say "write nothing to system temp": a worker created and deleted a throwaway file there and one in the project root. (3) A reviewer mutates tracked files; check file hashes before the owner commits mid-review. (4) A worker's claim about a leak (temp count +3) was wrong; the reviewer settled it by diffing names.
- Not done: the csvrow runs (control, path, no-plan, qwen3-worker), the conclusion, making the repo public.

#### agent-loop: fail-closed Docker tests, `capped` label, csvrow tests 6 to 11, csvrow runs (2026-10-30, committed by the owner as a856c32 and aae807c, pushed)
- Units: (0) real-Docker tests fail closed when Docker is down (`AGENT_LOOP_SKIP_DOCKER=1` opts out) plus a Python 3.12 note on `_RESERVED`; (A) an automatic `stop` at the rejection cap is result `capped`, only a real owner reply says "stopped by owner"; (B) five csvrow quoting tests (6 to 11), fixture pin changed; (C) re-score of the 10 saved control codes in the Docker sandbox, run by the Lead; (D) dashboard page and report word a flag reply as automatic, plus a test pinning the app.js wiring.
- Agents and models: worker x4 (haiku), reviewer x5 (opus; one before the orchestrate word was said). No researcher. Unit 0 was edited by the Lead before the owner said "orchestrate" and went to the reviewer afterwards; the Lead did one docstring fix inline at the owner's choice (no round 2 for it).
- Review rounds: unit 0 round 1 CHANGES_REQUESTED (stale docstring, fixed inline); A round 1 APPROVED; B round 1 APPROVED; D round 1 CHANGES_REQUESTED (nothing tested the app.js wiring; owner chose a worker), round 2 APPROVED.
- Owner decisions: label wording `capped`; tests first, then re-score the control, then `path`; fix the dashboard wording; leave unwrapped-quote cases unpinned; leave old traces as recorded; start `path` after the commit, then `no-plan` and `qwen3-worker`.
- Measured subagent tokens: workers 92,600 + 35,582 + 45,036 + 24,518 = 197,736; reviewers 26,294 + 52,326 + 46,240 + 44,022 + 19,536 = 188,418; total 386,154. Lead (main chat) tokens: not measured.
- Result (csvrow, 10 attempts each, 11-test fixture; the control re-scored from saved code): control 1/10 (the original report said 2/10 under 6 tests); path 1/10; no-plan 0/10; qwen3-worker 0/10. No errors. Decision table: d = 0, -1, -1, so all three variants DROP. Stray `pkg/csvrow/parse.py` writes: 3/10 control, 0/10 path. Mean seconds per attempt: about 29 control, 32 path, 27 no-plan, 53 qwen3-worker.
- Lessons: (1) A "stopped by owner" label on an automatic stop was wrong from the start of the eval harness; check who produced a decision before naming it. (2) Tests the models can pass by accident (a double toggle that cancels out) hide a bug until a model finds it; cross-check fixtures against a reference (here the csv module) and try plausible wrong implementations. (3) A static check on a UI file is worth a small test when no unit test covers the wiring (mutant survived node and pytest, caught only by a headless browser). (4) The Lead should not edit before the process is set; it did, and said so. (5) A fake `docker.cmd` does not shadow `docker.exe` for subprocess on Windows, so a fake must be an .exe.
- Deviations: none outside the project. Two 162 KB `.tmp` files appeared in the Windows temp folder during a reviewer's screenshot check; probably the tool harness's, not removed. The Lead did not read or delete anything in temp.
- Not done: the written conclusion of the csvrow experiment, more tests, making the repo public, relabelling old traces (left on purpose).

## Cost

### agent-loop, 2026-10-30 (raw per-agent numbers)

| Task | Role | Model | subagent_tokens (tool uses, wall time) |
| --- | --- | --- | --- |
| agent-loop csvrow diagnosis | researcher | haiku | 156,159 (62 tool uses, 281 s) |
| agent-loop reserved requirements | worker | haiku | 36,362 (27 tool uses, 190 s) |
| agent-loop reserved requirements | reviewer round 1 | opus | 28,379 (8 tool uses, 138 s) |
| agent-loop reserved requirements | worker pass 2 (unit 1) | haiku | 25,884 (16 tool uses, 98 s) |
| agent-loop reserved requirements | reviewer round 2 (unit 1) | opus | 20,026 (7 tool uses, 89 s) |
| agent-loop constraints file | worker (unit 2) | haiku | 70,212 (41 tool uses, 379 s) |
| agent-loop constraints file | reviewer round 1 (unit 2) | opus | 39,116 (17 tool uses, 287 s) |
| agent-loop verify pytest set | worker (unit 2b) | haiku | 165,593 (75 tool uses, 923 s) |
| agent-loop verify pytest set | reviewer round 1 (unit 2b) | opus | 74,916 (32 tool uses, 614 s) |
| agent-loop temp leak scan (unit 3) | researcher | haiku | 45,796 (18 tool uses, 69 s) |
| agent-loop review units 1, 2, 2b (round 1, fresh) | reviewer | opus | 62,679 (27 tool uses, 629 s) |
| agent-loop temp leak fix (unit 3) | worker | haiku | 54,657 (23 tool uses, 155 s) |
| agent-loop github profile lookup | researcher | haiku | 17,554 (9 tool uses, 54 s) |
| agent-loop review temp leak fix (unit 3, round 1) | reviewer | opus | 51,245 (17 tool uses, 248 s) |
| agent-loop verifier pycache fix (unit 3 rework) | worker (fresh) | haiku | 43,687 (26 tool uses, 239 s) |
| agent-loop review temp leak + verifier fix (unit 3, round 2) | reviewer | opus | 42,701 (20 tool uses, 374 s) |
| agent-loop dist-info test + README wrap (unit 3 follow-up) | worker (fresh) | haiku | 36,712 (17 tool uses, 160 s) |
| agent-loop prompt_variants option (csvrow variants) | worker | haiku | 88,635 (43 tool uses, 372 s) |
| agent-loop record prompt_variants in eval reports | worker | haiku | 38,432 (22 tool uses, 165 s) |
| agent-loop review prompt_variants + report (round 1) | reviewer | opus | 53,762 (28 tool uses, 775 s) |
| agent-loop rework of prompt_variants (round 1 findings) | worker (fresh) | haiku | 89,165 (57 tool uses, 517 s) |
| agent-loop review prompt_variants + report (round 2) | reviewer | opus | 46,055 (28 tool uses, 1,145 s) |
| agent-loop review real-Docker tests fail closed (round 1) | reviewer | opus | 26,294 (10 tool uses, 193 s) |
| agent-loop capped result label (unit A) | worker | haiku | 92,600 (46 tool uses, 256 s) |
| agent-loop review capped label (unit A, round 1) | reviewer | opus | 52,326 (25 tool uses, 810 s) |
| agent-loop csvrow tests 6 to 11 + repin (unit B) | worker | haiku | 35,582 (20 tool uses, 204 s) |
| agent-loop review csvrow tests (unit B, round 1) | reviewer | opus | 46,240 (20 tool uses, 245 s) |
| agent-loop dashboard wording for flag replies (unit D) | worker | haiku | 45,036 (22 tool uses, 157 s) |
| agent-loop review dashboard wording (unit D, round 1) | reviewer | opus | 44,022 (19 tool uses, 239 s) |
| agent-loop pin app.js reply-title wiring test (unit D rework) | worker (fresh) | haiku | 24,518 (10 tool uses, 127 s) |
| agent-loop review dashboard wording (unit D, round 2) | reviewer | opus | 19,536 (5 tool uses, 125 s) |
