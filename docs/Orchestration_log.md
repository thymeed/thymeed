# Orchestration log

How each project was built with a multi-agent Claude Code workflow: units, review rounds, findings, measured tokens and lessons. Each project has its own file in [`runs/`](runs/), with the run entries first and the raw per-agent cost rows after them.

The agent definitions are in [`agents/`](../agents/). The general team (Lead, planner, researcher, worker, reviewer) runs most projects. combinatorics-discovery uses its own team.

## Projects

| Project | What it is | Repo | Dates | Log |
|---|---|---|---|---|
| Squirrow | Local-first memory manager for AI chats | [public](https://github.com/thymeed/Squirrow) | 2026-10-21 to 10-31 | [squirrow.md](runs/squirrow.md) |
| FocusPet | Desktop companion with local LLM chat | private | 2026-10-22 to 11-01 | [focuspet.md](runs/focuspet.md) |
| agent-loop | Lead, worker, check and reviewer loop on local models | private | 2026-10-30 | [agent-loop.md](runs/agent-loop.md) |
| singularity | Agent runtime with a shell gate and a real-model eval | local only | 2026-11-10 to 11-11 | [singularity.md](runs/singularity.md) |
| combinatorics-discovery | Verified results in computational combinatorics (own agent team) | local only | 2026-11-05 to 11-11 | [combinatorics-discovery.md](runs/combinatorics-discovery.md) |
| personal-site | Personal site and its rendered hero scene | not published | 2026-11-09 | [personal-site.md](runs/personal-site.md) |
| night-city | Apartment scene redesign, stage 1 | not published | 2026-11-01 | [night-city.md](runs/night-city.md) |
| Research repo agenda | One coordinator session in a private research repo | private | 2026-10-23 | [research-repo.md](runs/research-repo.md) |
| Agent team tests | Smoke tests of the team in scratch folders | none | 2026-10-23 to 10-28 | [agent-team-tests.md](runs/agent-team-tests.md) · [full notes](agent-team-smoke-tests.md) |

## Setup history

The six smoke tests ran the planner-subagent version of the team. Run 5 in [focuspet.md](runs/focuspet.md) was the first real run with the Lead setup, where the main chat plans by following [Lead.md](../agents/general/Lead.md), so it counts as test 7. Test 8 is in [agent-team-tests.md](runs/agent-team-tests.md).

## How the numbers are recorded

- Token figures are `subagent_tokens` as each agent reports them. The Lead (main chat) is not counted unless a line says so.
- Nothing is estimated. A figure the harness did not report is written as "not measured".
- Resumed agents can report cumulative figures. Entries say which figure was taken.
- ccusage figures, where they appear, are list-price estimates for a whole session or day and are kept apart from subagent figures.

## Adding a project or a run

1. New project: copy [`runs/_template.md`](runs/_template.md) to `runs/<project>.md` and add one row to the table above.
2. New run: add an entry under `## Runs` in that project's file, and its per-agent rows under `## Cost`.
3. Update the Dates column for that project.
