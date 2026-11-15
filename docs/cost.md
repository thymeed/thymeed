# Cost log

Per-agent token figures now live in each project's file, under `## Cost`, next to the runs they belong to.

| Project | Cost rows |
|---|---|
| Squirrow | [runs/squirrow.md#cost](runs/squirrow.md#cost) |
| FocusPet | [runs/focuspet.md#cost](runs/focuspet.md#cost) |
| agent-loop | [runs/agent-loop.md#cost](runs/agent-loop.md#cost) |
| singularity | [runs/singularity.md#cost](runs/singularity.md#cost) |
| combinatorics-discovery | [runs/combinatorics-discovery.md#cost](runs/combinatorics-discovery.md#cost) |
| personal-site | [runs/personal-site.md#cost](runs/personal-site.md#cost) |
| night-city | [runs/night-city.md#cost](runs/night-city.md#cost) |
| Agent team tests | [runs/agent-team-tests.md#cost](runs/agent-team-tests.md#cost) |

**Method.** Figures are subagent token usage as reported by Claude Code (`subagent_tokens`). The parent session's own tokens are not included, because the session does not report them per task. Rows marked pending were still running when written. Nothing is estimated; missing figures say "not measured". See [Orchestration_log.md](Orchestration_log.md#how-the-numbers-are-recorded) for the full rules.
