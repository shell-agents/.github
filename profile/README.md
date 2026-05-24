<div align="center">

# shell-agents

**Small, sharp agents that live where work happens — your shell.**

A safe-by-construction, composable suite of CLI agents and the
hub-and-controller that keeps them honest.

[Manifesto](https://github.com/shell-agents/.github/blob/main/MANIFESTO.md) ·
[Spec](https://github.com/shell-agents/.github/blob/main/AGENT_SPEC.md) ·
[Safety](https://github.com/shell-agents/.github/blob/main/SAFETY.md) ·
[Contribute](https://github.com/shell-agents/.github/blob/main/CONTRIBUTING.md)

![License](https://img.shields.io/badge/license-Apache--2.0-blue)
![Spec](https://img.shields.io/badge/spec-v0.1--draft-orange)
![Status](https://img.shields.io/badge/status-early-yellow)

</div>

---

## What this is

`shell-agents` is a home for **terminal-native AI agents** that follow
a shared protocol, declare their blast radius honestly, and route
through a single controller you can audit and kill at any time.

One front door (the **Hub**). One brain (the **Controller**). A growing
fleet of specialists (the **Agents**) — each does one thing well.

```
   ┌─────────────┐
   │  Agent Hub  │   you talk here
   └──────┬──────┘
          │
   ┌──────▼──────────┐
   │ Agent-Controller│   routes, confirms, audits
   └──┬───┬───┬───┬──┘
      │   │   │   │
     git logs k8s aws   one capability each
```

## Why we exist

AI agents are running real shell commands on real machines today.
Most of them have no shared protocol, no blast-radius model, no audit
trail, and no kill switch. That's a problem with a deadline.

We're not building another framework. We're building the **opinionated
runtime** for safe, composable, observable shell agents — and a small
set of reference agents that prove the model works.

## Principles, in one breath

1. The shell is the universal interface.
2. One agent, one job.
3. Composable beats monolithic.
4. Safe by construction, not by hope.
5. Observable or it didn't happen.
6. Local-first, cloud-optional.
7. Open contracts, Apache-2.0, no rug pulls.
8. We say no.

Full text: [MANIFESTO.md](https://github.com/shell-agents/.github/blob/main/MANIFESTO.md).

## Repos

> Status legend: 🟢 stable · 🟡 active development · 🔵 spec/draft · ⚪ planned

| Repo | Status | What it is |
|------|--------|------------|
| [`.github`](https://github.com/shell-agents/.github) | 🔵 | Org-wide spec, safety charter, contributing guide |
| [`agent-template`](https://github.com/shell-agents/agent-template) | 🔵 | Starter kit for a spec-conformant agent |
| `hub` | ⚪ | The user-facing front door (TUI + lib) |
| `controller` | ⚪ | The orchestrator + safety enforcer |
| `git-recover` | ⚪ | Reference agent: recovers lost git state |

The full roadmap lives in [ROADMAP.md](https://github.com/shell-agents/.github/blob/main/ROADMAP.md).

## Quick start

```bash
# (placeholder — nothing to install yet)
# Once hub + controller ship:

curl -fsSL get.shell-agents.dev | sh
shell-agents init
shell-agents add git-recover
shell-agents
```

## Build an agent

Anything that speaks our protocol can join the suite. The bar:

- Implements the four required methods (`describe`, `invoke`, `cancel`, `health`)
- Ships an honest `agent.toml` manifest with declared blast radius
- Logs every command through the controller — no backchannels
- Apache-2.0 licensed, DCO-signed

Full contract: [AGENT_SPEC.md](https://github.com/shell-agents/.github/blob/main/AGENT_SPEC.md).
Submission flow: [CONTRIBUTING.md](https://github.com/shell-agents/.github/blob/main/CONTRIBUTING.md).

## What we will not do

- Ship agents that can't be inspected, paused, or killed
- Add a `--yolo` flag, ever
- Ship a chat UI pretending to be an agent
- Wrap `curl` and call it AI

We mean it.

## Contributing

We want sharp, opinionated, well-scoped agents. Start with an
[Agent Proposal](https://github.com/shell-agents/.github/blob/main/CONTRIBUTING.md#how-to-propose-a-new-agent) —
not a 5,000-line PR. We respond within 7 days.

## Security

Found a way to bypass the controller, smuggle an unlogged command, or
escalate blast radius without confirmation? Don't open a public issue.
See [SAFETY.md §8](https://github.com/shell-agents/.github/blob/main/SAFETY.md#8-reporting-a-vulnerability).

## License

Everything under this org is **Apache-2.0**, unless a repo's `LICENSE`
file says otherwise (it won't).

---

<div align="center">
<sub>shell-agents · spec v0.1 · Apache-2.0 · est. 2026</sub>
</div>
