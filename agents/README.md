# Agent definitions

These are the Claude Code agent files behind the [Orchestration log](../docs/Orchestration_log.md). Each `.md` file is one agent: the front matter sets its model and tools, and the body is its instructions. Paths inside them (such as `~/.claude/orchestration/`) are where the files live on my machine.

## General team

Used for most projects. The Lead is the main chat, not a subagent.

| Agent | Model | Job |
|---|---|---|
| [Lead](general/Lead.md) | Sonnet (main chat) | Asks me the product questions, splits work into units, writes briefs, runs the review loop |
| [planner](general/planner.md) | Opus, medium effort | Read-only. Splits a phase into small units with one proving check each |
| [researcher](general/researcher.md) | Haiku (Sonnet for precedent research in other people's repos) | Read-only lookups with citations |
| [worker](general/worker.md) | Haiku (Sonnet for risky code) | Implements exactly one unit, with mutation and boundary checks before review |
| [reviewer](general/reviewer.md) | Opus | Reads every diff, runs its own tests, returns APPROVED or CHANGES_REQUESTED |

## combinatorics-discovery team

A separate pipeline for verified results in computational combinatorics.

| Agent | Model | Job |
|---|---|---|
| [director](combinatorics/director.md) | Sonnet | Picks targets, sets compute caps, routes units, escalates after failed reviews |
| [harvester](combinatorics/harvester.md) | Haiku | Collects candidate problems from licensed open sources |
| [prior-art](combinatorics/prior-art.md) | Haiku | Novelty gate: searches for existing results and certificates |
| [encoder](combinatorics/encoder.md) | Haiku | Writes CNF encodings and tests them on known values first |
| [searcher](combinatorics/searcher.md) | Haiku | Runs searches and solvers within a compute cap, saves every witness |
| [reviewer](combinatorics/reviewer.md) | Opus, medium effort | Checks evidence level, independent checkers, licences and the ledger entry |
