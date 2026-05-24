# shell-agents Agent Specification (v0.1)

This document defines the contract every agent in the `shell-agents`
org must satisfy. If your agent doesn't meet this spec, it doesn't
ship under this org — no exceptions.

> Status: **Draft v0.1**. Breaking changes allowed until v1.0.
> The spec is versioned independently of any agent.

---

## 1. What is a shell-agent?

A **shell-agent** is a program that:

1. Runs as a process (binary, script, container — your choice)
2. Speaks the **Agent Protocol** (§3) over stdio or a local socket
3. Declares its **capabilities** and **blast radius** in a manifest (§4)
4. Logs every action it takes in a replayable form (§5)

That's it. No language requirement. No framework requirement.
A 50-line bash script that follows the spec is a valid shell-agent.

---

## 2. The three roles

```
   ┌─────────────┐
   │  Agent Hub  │   user-facing — one front door
   └──────┬──────┘
          │  Agent Protocol
   ┌──────▼──────────┐
   │ Agent-Controller│   routes, orchestrates, enforces safety
   └──┬───┬───┬───┬──┘
      │   │   │   │  Agent Protocol
     A1  A2  A3  A4   specialized agents
```

| Role | Responsibility | Ships in |
|------|----------------|----------|
| **Hub** | User interaction, session, history, UI | `shell-agents/hub` |
| **Controller** | Routing, orchestration, safety enforcement | `shell-agents/controller` |
| **Agent** | One capability, executed safely | any repo following this spec |

A repo can implement one or more roles. The roles communicate
through the Agent Protocol — never through shared memory, files,
or implicit state.

---

## 3. The Agent Protocol

JSON-RPC 2.0 over stdio (default) or a Unix domain socket.
Newline-delimited. UTF-8.

### 3.1 Required methods every agent must implement

| Method | Purpose |
|--------|---------|
| `agent.describe` | Return the agent manifest (§4) |
| `agent.invoke` | Run a capability with input, return output |
| `agent.cancel` | Cancel an in-flight invocation |
| `agent.health` | Liveness + readiness check |

### 3.2 `agent.invoke` shape

```json
{
  "jsonrpc": "2.0",
  "id": "01J...",
  "method": "agent.invoke",
  "params": {
    "capability": "git.recover",
    "input": { "description": "I lost my last 3 commits" },
    "context": {
      "cwd": "/Users/foo/project",
      "session_id": "01J...",
      "dry_run": false
    }
  }
}
```

### 3.3 Streaming
Long-running invocations stream events back as JSON-RPC
notifications: `agent.event` with `{ kind, data }`. Kinds:
`log`, `progress`, `command`, `result`, `error`.

### 3.4 Cancellation
Every invocation MUST be cancellable within 2 seconds.
An agent that ignores `agent.cancel` is non-conforming.

---

## 4. The Agent Manifest

Every agent ships an `agent.toml` at its repo root:

```toml
[agent]
name        = "git-recover"
version     = "0.3.1"
description = "Recovers lost commits, stashes, and branches."
license     = "Apache-2.0"
homepage    = "https://github.com/shell-agents/git-recover"

[runtime]
entrypoint = ["./git-recover", "--stdio"]
language   = "go"
sandbox    = "recommended"   # one of: required | recommended | optional

[[capabilities]]
id          = "git.recover"
summary     = "Recover lost git state from reflog"
inputs      = "schema/recover.input.json"
outputs     = "schema/recover.output.json"
blast       = "low"          # see §6
side_effects = ["fs.read", "git.write"]

[[capabilities]]
id          = "git.diagnose"
summary     = "Diagnose a broken git state without writing"
blast       = "none"
side_effects = ["fs.read"]
```

The manifest is the single source of truth. The hub, controller,
and any UI render directly from it. Lying in the manifest
(declaring `blast = "none"` while running `rm`) is grounds for
removal from the org.

---

## 5. Observability requirements

Every agent MUST emit, for every command it executes:

```json
{
  "ts": "2026-05-24T13:16:40Z",
  "session_id": "01J...",
  "agent": "git-recover@0.3.1",
  "capability": "git.recover",
  "command": "git reflog expire --expire=now --all",
  "cwd": "/Users/foo/project",
  "blast": "low",
  "approved_by": "user|policy|none",
  "result": "ok|error|cancelled",
  "duration_ms": 142
}
```

These events flow through the controller and into the hub's
audit log. An agent that bypasses this channel is non-conforming.

---

## 6. Blast radius levels

Every capability declares its worst-case impact:

| Level | Meaning | Default behavior |
|-------|---------|------------------|
| `none` | Read-only, idempotent | Auto-allow |
| `low` | Local writes, reversible | Auto-allow with log |
| `medium` | Local writes, hard to reverse (history rewrites, deletions) | Confirm |
| `high` | Network, shared state, secrets | Confirm + audit |
| `critical` | Production, payments, destructive ops on shared infra | Confirm + audit + cooldown |

The controller enforces these. Agents request — the controller
decides. Agents that try to bypass the controller's confirmation
flow are non-conforming.

Full details live in [SAFETY.md](./SAFETY.md).

---

## 7. Naming and identity

- Agent names use lowercase, dot-separated, capability-first:
  `git.recover`, `k8s.tail`, `aws.audit`.
- Repo names mirror the primary capability namespace:
  `shell-agents/git-recover`, not `shell-agents/recover-things`.
- Agent versions follow SemVer. Breaking the manifest schema =
  major bump.

---

## 8. Conformance

An agent claims conformance by:

1. Passing the `shell-agents/conformance` test suite (TBD)
2. Including a `# shell-agents-spec: 0.1` line in its README
3. Publishing its manifest at a stable URL or in-repo path

We will publish a conformance badge once v1.0 lands.

---

## 9. What this spec deliberately does NOT cover

- The agent's internal architecture (LLM-backed, rule-based, both, neither)
- The agent's choice of language, runtime, or build system
- Authentication to third-party services (lives in the agent)
- The hub's UX (it's free to be a TUI, web app, IDE extension, …)

The spec is a wire contract. What runs behind that wire is yours.

---

## 10. Versioning this spec

| Version | Status | Date |
|---------|--------|------|
| 0.1 | Draft | 2026-05-24 |

Changes go through the [GOVERNANCE](./GOVERNANCE.md) process.
Until v1.0, breaking changes are allowed but must bump the minor
version and ship a migration note.
