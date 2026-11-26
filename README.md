# Thymeed

I build local-first apps, AI agent tooling and small, pre-registered research projects. I build with AI coding agents under a review-and-test loop I control.

## Projects

- **[agent-workshop](https://github.com/thymeed/agent-workshop)** · Electron: a desktop dashboard that shows how a multi-agent coding session routes work across models. [Download](https://github.com/thymeed/agent-workshop/releases/latest)
- **[agent-loop](https://github.com/thymeed/agent-loop)** · Python + LangGraph: a lead, worker and reviewer loop on local Ollama models. Model-written code runs in a locked-down Docker sandbox and cannot pass a failing test.
- **[Squirrow](https://github.com/thymeed/Squirrow)** · React + TypeScript: a local-first memory manager for AI chats. No account needed, and your data stays in your browser. [Live demo](https://squirrow.vercel.app/)
- **[autolab](https://github.com/thymeed/autolab)**: agents that plan, run and review small, pre-registered ML experiments.

## Research

- [![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.24189909.svg)](https://doi.org/10.5281/zenodo.24189909) **Three-colour Rado numbers for ax + by = cz with three distinct coefficients** (preprint, v3.1, 2026): exact values of R₃ for 73 equations, with upper bounds backed by LRAT proofs from two independent SAT encodings and checked by the formally verified checker cake_lpr.
- **[ssm-state-quantization](https://github.com/thymeed/ssm-state-quantization)**: pre-registered experiments on rounding Mamba-2's recurrent state.
- **[replication-criterion](https://github.com/thymeed/replication-criterion)**: a pre-registered feasibility analysis of machine self-replication.

## Merged open-source work

- [goose #12993](https://github.com/aaif-goose/goose/pull/12993), [#13487](https://github.com/aaif-goose/goose/pull/13487): custom providers keep image support after a rename, with one vision lookup shared by the SDK and provider registry
- [repowise #4729](https://github.com/repowise-dev/repowise/pull/4729), [#4806](https://github.com/repowise-dev/repowise/pull/4806), [#4350](https://github.com/repowise-dev/repowise/pull/4350): Dapr pub/sub topic extraction across five languages, plus a flaky-test fix
- [cubrid-mcp-server #369](https://github.com/cubrid-lab/cubrid-mcp-server/pull/369), [#374](https://github.com/cubrid-lab/cubrid-mcp-server/pull/374): config tests
- [octofs #16](https://github.com/Muvon/octofs/pull/16): first unit tests for `request_ctx` (Rust)

Rust · TypeScript · Python · Tauri · Electron · React · LangGraph · Docker · Ollama
