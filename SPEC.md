# Service Requirements

Status: Draft v1 (language-agnostic)

Motivation: Define a bleeding-edge modern agent harness.

**1. Schema (shared types, no logic)**

- Data shapes: messages, IDs, permission rules, events (including summaries and checkpoints).
- Tool-call contract: the shape of a call, its arguments and its result.

**1.1. Protocol**

Client-visible events plus commands (prompt, cancel, steer, approval answers).

**2. Configuration (needs Schema)**

- Layered precedence, plus the trust boundary: project config can't set permissions or model.
- Defines model, effort, credential source, storage paths, workspace, enabled tools, MCP servers and skills (and their load paths), and limits.

**3. Peers (each needs Schema and Config)**

**3.1. Model gateway:**

- Connects to providers, authenticates, streams, handles network errors.
- Prompt caching and provider quirks.
- Model metadata (context window, pricing) and token/cost accounting.
- Depends only on a small provider-settings slice of Config (model, effort, credential source).

**3.2. Session log:** the ordered, durable, append-only record of everything that happened.

- Interface: `append(event)`, `read(from cursor)`, and optional live subscribe.
- Holds messages, tool results, summaries, checkpoint refs, and lifecycle events for background processes and subagents (started, finished, failed, interrupted) with IDs and links. Never live handles, buffers or large output (those are refs to files).
- Makes resume, crash recovery, replay and client reconnect, and Undo/Redo possible. It makes no decisions.
- In disk (Postgres or Sqlite)

**4. Safety / Guardian (needs 1, 2, and 3)**

- Permissions policy: modes, allow/deny rules, approval state machine, treating tool output as untrusted.
- Model-based injection check, which calls the gateway.
- Sandbox enforcement, kept separate from policy.
- It evaluates tool calls using the contract from 1, not any concrete tool, and reports decisions to the log.
- Modes can be Ask for approval (user input), Approve for me (auto review), or Full access (Unrestricted).

**5. Tool dispatch (needs 1, 4 and the session log)**

- Receives a call, validates input, asks Safety, then runs the tool or returns a denial.
- Formats results, handles parallel and background runs, and appends results to the log.
- Checkpoints before mutating calls (and every shell call): the log stores the checkpoint ref, the snapshot mechanism (git or shadow copy) runs on the environment interface.
- Owns the registry of triggering background processes (e.g. background or parallel subagents for delegation).
- Revert (snapshots/undo) restores files and appends a revert event; the log is never edited.

**6. Concrete tools (need 5)**

- File read/edit, search, shell (uses the sandbox), web, ask-user, custom commands...
- MCP servers as another tool source, and the skill-instructions read tool.
- The subagent tool is the forward reference, since it needs the loop. Inject it late.

**7. Context management (needs 2, 3 and 5)**

- Assembles the prompt: system prompt, tool definitions, skill names and descriptions, AGENTS.md...
- Selects history by reading the log, truncates tool output, compacts (appending the summary to the log), and enforces the token budget using gateway model metadata.

**8. Agent loop (needs everything above)**

- Model → tool → result cycle, with retries, cancellation and completion.
- Execution limits (steps, wall-clock, cost), mid-run human input (steer and queue), agent modes (build, plan, subagent), per-session concurrency.
- Fires hooks.

**9. Server adapters (needs 8 and Protocol)**

- Translate: Core results become protocol messages, and commands become Core calls.
- Carry: put them on a transport (HTTP/SSE, stdio, stdout, in-process).
- Expose the log to clients, with cursor-based reconnect.

**10. Clients (needs Protocol only, never Core)**

- Render text, tool activity, progress and errors.
- Show approval prompts and capture input.
- Hold no product state.
- Any device, desktop, terminal, mobile, SDK...

**Cross-cutting (any time after 1)**

- **Observability (runtime):** logs, metrics, tracing, diagnostics. It can be another reader of the session log.
- **Verification (dev-time):** unit and e2e tests, evals, benchmarks, fuzzing, replay-based regression.

## Legibility

The more feedback the better. Increasing agent legibility meaningfully improves output quality. Custom linters, mutation tests, structural tests, pre-push hooks, LSPs... these integrate important feedback sensors into the agent workflows. It is also of value to attempt avoiding contradicting guides and sensors or pointing the agent in multiple directions at once.
