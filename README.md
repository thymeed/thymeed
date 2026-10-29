# Hi, I'm Thymeed

**Software Developer · AI tools, agent workflows and evals · local-first apps**

I build with a multi-agent Claude Code workflow. The main Sonnet session leads: it asks me the product questions first, then splits each change into small units. Haiku workers make the edits, an Opus reviewer blocks on missing requirements, untested behavior and silent data loss, and I decide what gets fixed. Nothing merges until CI is green and I've tested it by hand.

Every run is logged with measured tokens and review rounds: **[Orchestration log →](https://github.com/thymeed/thymeed/blob/main/docs/Orchestration_log.md)** · **[Cost log →](https://github.com/thymeed/thymeed/blob/main/docs/cost.md)** · **[Smoke tests →](https://github.com/thymeed/thymeed/blob/main/docs/agent-team-smoke-tests.md)**

---

## Open-source contributions
- [goose #12993](https://github.com/aaif-goose/goose/pull/12993): fixed custom providers losing image support when renamed, by looking up vision through their catalog ID, with a mock-server test for each override case (merged)
- [cubrid-mcp-server #369](https://github.com/cubrid-lab/cubrid-mcp-server/pull/369): added a full truth-table test for `parse_bool` (merged)
- [cubrid-mcp-server #374](https://github.com/cubrid-lab/cubrid-mcp-server/pull/374): pinned the rule that named connections don't inherit default settings, with a mutation check (merged)
- [repowise #4350](https://github.com/repowise-dev/repowise/pull/4350): stopped the repo timeout test flaking on slow runners (merged)
- [repowise #4729](https://github.com/repowise-dev/repowise/pull/4729): added Dapr pub/sub topic extraction for .NET (`[Topic]` and `PublishEventAsync` on `DaprClient`) and `kind: Subscription` YAML manifests (merged)
- [octofs #16](https://github.com/Muvon/octofs/pull/16): added the first unit tests for `request_ctx` (hint dedupe, scoping, view cache), my first merged Rust PR (merged)

---

## Projects

### FocusPet · Tauri 2 + Rust · Windows
A desktop companion with local LLM chat through Ollama, so conversations never leave your machine. Network access is off by default.

**Evals:** a seeded harness (gemma3:4b, 15 prompts × 3 seeds) took tool-call replies from 24/45 to 42/45. When fixing a memory bug, I shipped a read-only note filter instead of a higher-scoring version that deleted users' real notes. **[Case study →](https://github.com/thymeed/thymeed/blob/main/docs/Orchestration_log.md#run-5-focuspet-34-state-number-rule-2026-10-24)**

*Private repo. Demo on request.*

### [agent-workshop](https://github.com/thymeed/agent-workshop) · Electron + Node
A desktop dashboard that shows how a multi-agent coding session routes work across Opus, Sonnet and Haiku. It has zero runtime dependencies. CI builds Windows, macOS and Linux installers for every release, with SHA-256 checksums.
**[Download the latest release →](https://github.com/thymeed/agent-workshop/releases/latest)**

![License MIT](https://img.shields.io/badge/license-MIT-green) ![Latest release v1.4.1](https://img.shields.io/badge/release-v1.4.1-blue) ![CI: Windows, macOS, Linux](https://img.shields.io/badge/CI-Windows%20%7C%20macOS%20%7C%20Linux-informational)

![Agent Workshop](https://raw.githubusercontent.com/thymeed/agent-workshop/master/docs/preview.png)

### [Squirrow](https://github.com/thymeed/Squirrow) · React 19 + TypeScript
A local-first memory manager for your AI chats. It needs no account, and your data stays in your browser. It installs as an offline app.
**[Try it live](https://squirrow.thymeed.workers.dev)** · **[How it was built →](https://github.com/thymeed/thymeed/blob/main/docs/Orchestration_log.md#run-7-squirrow-five-features-stacked-prs-2026-10-27-to-10-28-merged-as-60-61-64-65-66)**

![License MIT](https://img.shields.io/badge/license-MIT-green) ![Latest release v1.1.0](https://img.shields.io/badge/release-v1.1.0-blue) ![CI: Ubuntu](https://img.shields.io/badge/CI-Ubuntu-informational)

![Squirrow](docs/og.png)

### agent-loop · Python + LangGraph · local models
A lead → worker → check → reviewer loop that runs fully on local Ollama models (gemma3:4b plans, qwen2.5-coder:7b writes and reviews). Model-written code runs in a locked-down Docker sandbox with no network and a read-only filesystem, and the reviewer can't approve a failing test run. After 3 rejections, a stronger model gets one attempt before it pauses and asks me. Runs are checkpointed to SQLite and can resume after a crash.

It also has a research mode that answers from recent arXiv papers, with a check that every citation was actually fetched and every quote appears word for word in the abstract. **In progress:** project mode, which edits a real multi-file Python project and gates the result on its own pytest suite.

*Private repo. Demo on request.*

---

## Research

- **ssm-state-quantization**: how rounding the recurrent state of Mamba-2 (130M and 370M) hurts accuracy, broken down by how fast each head forgets. Every experiment is pre-registered in the log before it runs. *Private repo. Demo on request.*

  ![License MIT](https://img.shields.io/badge/license-MIT-green) ![Experiments, two small models](https://img.shields.io/badge/type-experiments-informational)
- **[replication-criterion](https://github.com/thymeed/replication-criterion)**: a pre-registered feasibility analysis of machine self-replication.

  ![License CC BY 4.0](https://img.shields.io/badge/license-CC%20BY%204.0-lightgrey) ![Study, no experiment](https://img.shields.io/badge/type-study-informational)

---

## Stack

**Languages:** Rust · TypeScript · JavaScript/Node · Python

**Frameworks:** Tauri 2 · Electron · Win32 · React · Vite · LangGraph

**Data & infra:** SQLite · IndexedDB · Docker · GitHub Actions

**AI tools:** Claude Code · Cursor · Ollama · MCP · pytest
