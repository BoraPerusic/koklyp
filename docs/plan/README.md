# koklyp — plan (synthesis)

Synthesised planning docs for koklyp, distilled from the four parallel agent drafts under `docs/{claude,gpt,gemini,minimax}/`.

| Doc | What it is |
|---|---|
| [`koklyp-architecture.md`](koklyp-architecture.md) | The architectural blueprint. Layered model, kernel ABI, module layout, agent loop, persistence, plugin model (PF4J + ACR), LLM provider, security, MCP integration, web console, boot sequence. **Updated after brainstorming responses.** |
| [`koklyp-features.md`](koklyp-features.md) | The long-list of features tagged v1 / v2 / L. **Updated after brainstorming responses.** |
| [`koklyp-brainstorming.md`](koklyp-brainstorming.md) | Original opinionated working document — pushbacks, bets, open questions. Kept as a historical record; the questions in §3 and §6 are now answered in `koklyp-brainstorming-responses.md`. |
| [`koklyp-brainstorming-responses.md`](koklyp-brainstorming-responses.md) | **Source of truth for closed decisions.** The user's answers to the brainstorming questions. Architecture and features have been re-aligned to this. |
| [`agent-diff.md`](agent-diff.md) | Detailed comparison of what each of the four agents (Claude, GPT, Gemini, MiniMax) produced, where they converged, where they diverged, what the synthesis took from each. |
| **v1 docs (current focus):** | |
| [`v1-specs.md`](v1-specs.md) | The v1 scope contract. In-scope, out-of-scope, NFRs, milestones, acceptance criteria, risks, bets. |
| [`v1-architecture.md`](v1-architecture.md) | The v1 wiring diagram. Kernel ABI in Kotlin, plugin ABI, SQLite DDL + Flyway plan, config schema, dispatcher state machine, plugin lifecycle, OCI/ACR flow, receipts format, web console API, MCP transports, boot sequence, error taxonomy, security check sequencing. |
| [`v1-tasks.md`](v1-tasks.md) | The detailed task list. ~95 tasks across 11 milestones (M0–M10 + cross-cutting), with dependencies, sizes, acceptance criteria. PF4J spike is M6.T1. |

## Decisions baked in (after `koklyp-brainstorming-responses.md`)

1. **JVM only.** Kotlin/Native dropped. GraalVM native-image stays open as a v2 distribution-format optimisation.
2. **No WASM.** Plugins are JVM JARs loaded via **PF4J**, distributed via OCI container registry (Azure Container Registry in the user's case). Cross-language extensions go through MCP servers.
3. **Single user, one process.** No multi-tenancy. `TenantScope` wrapper dropped.
4. **v1 channels: Web Console + CLI + Telegram.** Email / Slack / WhatsApp deferred to v2+.
5. **v1 LLM providers: one OpenAI-compatible client (BYOK).** The user's internal LLM Gateway speaks OpenAI; same client also drives OpenAI / Ollama / OpenRouter / Groq. Anthropic-native, provider router, fallback chain — all dropped from v1 (gateway handles upstream concerns).
6. **SOPs are v2.** v1 ships routines only.
7. **Plugin `signature_mode` default: `optional`** in v1.
8. **PF4J** picked directly (no hand-rolled URLClassLoader spike).
9. **Plugin distribution: OCI / ACR.** Plugins are first-party / internal-only for the foreseeable future.

## What's next

The v1 plan is concrete. Implementation order, in dependency-respected form, is in `v1-tasks.md` §"Dependency-respecting build order". Recommended starting point: **M0 Foundations** end-to-end before forking into M1 (memory) and M2 (loop) in parallel; PF4J spike (`M6.T1`) starts as soon as `M0.T6` (the `plugin-api` module) is done.
