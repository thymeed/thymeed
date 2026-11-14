# Thymeed

I build local-first apps and AI agent tooling. I direct a team of Claude Code agents (a Sonnet lead, Haiku and Sonnet workers, an Opus reviewer) and decide what ships. Nothing merges until the checks pass and I've tested it by hand.

## Merged open-source work

- [goose #12993](https://github.com/aaif-goose/goose/pull/12993): custom providers keep image support after a rename (vision is looked up by catalog ID)
- [repowise #4729](https://github.com/repowise-dev/repowise/pull/4729), [#4806](https://github.com/repowise-dev/repowise/pull/4806): Dapr pub/sub topic extraction for .NET, Java, JS/TS, Python and Go
- [repowise #4350](https://github.com/repowise-dev/repowise/pull/4350): fixed a timeout test that flaked on slow runners
- [cubrid-mcp-server #369](https://github.com/cubrid-lab/cubrid-mcp-server/pull/369), [#374](https://github.com/cubrid-lab/cubrid-mcp-server/pull/374): config tests for boolean aliases and connection inheritance
- [octofs #16](https://github.com/Muvon/octofs/pull/16): first unit tests for `request_ctx`, my first merged Rust PR

## Projects

- **[Squirrow](https://github.com/thymeed/Squirrow)** · React + TypeScript: local-first memory manager for AI chats. No account, and your data stays in your browser. [Live demo](https://squirrow.thymeed.workers.dev)
- **[agent-workshop](https://github.com/thymeed/agent-workshop)** · Electron: desktop dashboard of how a multi-agent coding session routes work across models. [Download](https://github.com/thymeed/agent-workshop/releases/latest)
- **[agent-loop](https://github.com/thymeed/agent-loop)** · Python + LangGraph: lead, worker and reviewer loop on local Ollama models. Model-written code runs in a locked-down Docker sandbox and cannot pass a failing test.
- **FocusPet** · Tauri 2 + Rust: desktop companion with local LLM chat. A seeded eval took tool-call replies from 24/45 to 42/45. *Private.* [Case study](docs/runs/focuspet.md#run-5-focuspet-34-state-number-rule-2026-10-24)
- **[autolab](https://github.com/thymeed/autolab)** · agents that plan, run and review small, pre-registered ML experiments. Round 1 is set up; no results yet.

In progress (private): singularity (a local agent harness with a live 3D view), Cyberspace (a Godot civilization sim where every resident thinks through a local model), and my personal site.

## Research

- [![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.24189909.svg)](https://doi.org/10.5281/zenodo.24189909) **Seventeen three-colour Rado numbers for ax + by = cz with three distinct coefficients** (preprint, 2026): exact values of R₃ for 17 equations. Upper bounds are certified by cake_lpr-verified LRAT proofs from two independent SAT encodings; lower bounds by explicit colourings.
- [ssm-state-quantization](https://github.com/thymeed/ssm-state-quantization): pre-registered experiments on rounding Mamba-2's recurrent state, and why the damage depends on how fast each head forgets.
- [replication-criterion](https://github.com/thymeed/replication-criterion): a pre-registered feasibility analysis of machine self-replication.

## How I work

Every run is logged with its review rounds and measured tokens: [Orchestration log](docs/Orchestration_log.md) · [Agent files](agents/)

Rust · TypeScript · Python · Tauri · Electron · React · LangGraph · Docker · Claude Code · Ollama
