Building a coding agent consists of several harness engineering concepts. An agent consists of Model + Harness. This project defines the systems that bring together emerging concepts from context and harness engineering to build a reliable and reusable agent simple and generic enough so it generalizes.

Such system needs to be designed with the expectations that models will keep getting smarter and so it allows us to underengineer significantly with fewer rules and more general mechanisms.

## Motivation

Define a modern agent harness that can be tuned to any specific use case and system.

> [!Note]
> This is a low-key engineering spec preview. Further references are needed as the tech evolves.

## Concepts

The core interface of mainstream coding agents has settled into a few common shapes:

- Model provider/gateway (connects to models, sends prompts, receives streamed responses, handles authentication, credentials, and network errors)
- Context management (builds prompts, includes conversation history and tool results, compacts old context, enforces resource limits)
- Memory and persistence (stores conversations, tool results, sessions, checkpoints, artifacts, and state needed to resume work)
- Agent loop/orchestrator (repeats model response → tool call → tool result, handles retries, cancellation, interruption, and completion)
- Tools and tool execution (reads and edits files, searches the workspace, runs commands, accesses web/MCP services, asks user interactively, loads skills, validates inputs, formats results, runs in background or parallel, enables subagents)
- Safety and permissions (checks tool calls before execution, applies allow/deny rules, requests approval, limits risky actions, and treats tool output as untrusted)
- Presentation (clients, shows model text, tool activity, progress, errors, prompts, and approval requests to the end consumer, which can be an API, terminal, native, or other UI)
- Configuration and runtime services (selects the model, provider, workspace, tools, MCP servers, skills, limits, and host capabilities)
- Observability and verification (provides evals, tests, diagnostics, logs, metrics, tracing, fuzzing, and failure reports)

See [SPEC.md](SPEC.md)

## Roadmap

[ ] Secure Sandboxing (filesystem, shell, repo, builds, tests...)
[ ] (Cloud) Orchestration

This kind of system can also be adapted and scaled to create fleets for agentic software factories, as seen in [openai/symphony](https://github.com/openai/symphony).

Good design is iterative.

## References

- [github.com/openai/codex](https://github.com/openai/codex)
- [github.com/anthropics/claude-code](https://github.com/anthropics/claude-code)
- [github.com/anomalyco/opencode](https://github.com/anomalyco/opencode)
- [github.com/earendil-works/pi](https://github.com/earendil-works/pi)
- [github.com/vercel-labs/fx](https://github.com/vercel-labs/fx)

## Related

- [github.com/alexeygrigorev/ai-engineering-field-guide](https://github.com/alexeygrigorev/ai-engineering-field-guide)
- [openai.com/index/harness-engineering](https://openai.com/index/harness-engineering)
- [martinfowler.com/articles/harness-engineering.html](https://martinfowler.com/articles/harness-engineering.html)
- [lilianweng.github.io/posts/2026-07-04-harness](https://lilianweng.github.io/posts/2026-07-04-harness)
- [unify.ai/blog/the-harness-is-not-enough](https://unify.ai/blog/the-harness-is-not-enough)
- [www.seangoedecke.com/llms-are-terrible-at-designing-ai-systems](https://www.seangoedecke.com/llms-are-terrible-at-designing-ai-systems)

## License

Licensed under the MIT License - see the [LICENSE file](LICENSE) for details.
