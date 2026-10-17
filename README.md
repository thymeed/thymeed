# thymeed · Windows desktop & local-first systems
**Software developer, AI-assisted.** I direct AI coding agents to build local-first desktop apps, then review, test and decide what ships. The code is AI-written. The scope, rules, reviews and release decisions are mine.

### Technical summary
- **Languages**: Rust, JavaScript/Node, TypeScript, Python, PowerShell
- **Desktop**: Tauri 2, Electron, Win32
- **Systems**: IPC, capability-gated tools, secret redaction, CI on Windows, macOS and Linux
- **Local AI**: Ollama integration, VRAM-aware model use, network access off by default
- **How I work**: a planner model hands work to agents with fixed lanes (worker for edits and tests, explorer and researcher read-only, advisor at decision points). Each repo has written rules agents read before touching code, and nothing merges without green CI and my sign-off. Tools: Cursor, Claude Code, Grok Bot.

### Selected work

**[Agent Workshop](https://github.com/thymeed/agent-workshop)** - Electron + Node dashboard that visualizes how a Cursor multi-agent session routes work. Live SSE with history replay, a loopback-only bridge with origin guards and secret redaction, and a transcript parser that reports format drift instead of going quiet. Zero runtime dependencies, three-platform CI, seven releases up to [v1.4.1](https://github.com/thymeed/agent-workshop/releases/tag/v1.4.1), with SHA-256 checksums since v1.3.0 (installers are unsigned).

![Agent Workshop](https://raw.githubusercontent.com/thymeed/agent-workshop/master/docs/preview.png)

![License MIT](https://img.shields.io/badge/license-MIT-green) ![Latest release v1.4.1](https://img.shields.io/badge/release-v1.4.1-blue) ![CI: Windows, macOS, Linux](https://img.shields.io/badge/CI-Windows%20%7C%20macOS%20%7C%20Linux-informational)

**[context-keeper](https://github.com/thymeed/context-keeper)** - local-first memory manager for AI chats. You save facts, decisions, preferences, snippets and file notes per project, then copy the ones you pick as paste-ready Markdown. Data stays in your browser's IndexedDB, with no backend, account or analytics. It is an installable offline PWA with JSON and Markdown export and import, built with React 19, TypeScript and Vite. Tests cover the core logic, the main UI flows and the service worker, with CI on Ubuntu and a tagged [v1.0.0](https://github.com/thymeed/context-keeper/releases/tag/v1.0.0). [Live app](https://context-keeper.thymeed.workers.dev).

![context-keeper](docs/context-keeper.png)

![License MIT](https://img.shields.io/badge/license-MIT-green) ![Latest release v1.0.0](https://img.shields.io/badge/release-v1.0.0-blue) ![CI: Ubuntu](https://img.shields.io/badge/CI-Ubuntu-informational)

**FocusPet** *(private, demo on request)* - Tauri 2 + Rust Windows companion. Local LLM chat through Ollama, Win32 window and screen sensing, and network access behind a capability gate that is off by default. Rust tests, Node and PowerShell verification scripts, Windows CI.


### Usage, from my own API dashboard (DeepSeek)
- Last 30 days (09/16 to 10/16): about **2.68 billion tokens, 10,299 requests, \$28.82**, roughly \$0.01 per million tokens.
- Peak day (Oct 14): **316M tokens, 1,274 requests, \$3.32**.
- The low rate reflects DeepSeek's pricing and caching, not a list price.

### On the side

**[SSM state quantization](https://github.com/thymeed/ssm-state-quantization)** - a small learning project: why does rounding Mamba-2's recurrent state to 8 bits nearly double perplexity? Experiments on the 130M and 370M models on one RTX 3070. The damage comes from heads that remember about 10 to 100 tokens, and from the sign pattern of round-to-nearest error, not its size. At 8 bits, stochastic rounding avoids it. The mechanism is still open, and the README says so.

![License MIT](https://img.shields.io/badge/license-MIT-green) ![Experiments, two small models](https://img.shields.io/badge/type-experiments-informational)

**[Self-replication feasibility study](https://github.com/thymeed/replication-criterion)** - can a machine reproduce itself without a data center or a reactor? A pre-registered, evidence-graded analysis with its corrections left visible. It is analysis and arithmetic, not a lab result.

![License CC BY 4.0](https://img.shields.io/badge/license-CC%20BY%204.0-lightgrey) ![Study, no experiment](https://img.shields.io/badge/type-study-informational)

Private research notes cover related feasibility questions, written the same way.

### Contact
GitHub: [@thymeed](https://github.com/thymeed)
