# shell-agents Safety Charter

An agent that can run `rm -rf` is one bad prompt away from a bad day.
This document is how we make sure that bad day never happens under
the `shell-agents` banner.

Safety here is not a feature. It is a **build-time property** every
agent must satisfy to ship in this org.

> Status: **v0.1**, versioned alongside [AGENT_SPEC.md](./AGENT_SPEC.md).

---

## 1. The five rules

Every agent in this org follows these. No exceptions, no opt-outs.

1. **Declare your blast radius.** Honestly. In the manifest. Per capability.
2. **Default to dry-run.** Destructive actions show what they would do
   before doing it. The user opts in to execution.
3. **Pass through the controller.** No agent runs a shell command
   without the controller seeing it first. No backchannels.
4. **Be cancellable.** Every action interrupts within 2 seconds.
   A wedged agent is an unsafe agent.
5. **Log everything, attribute everything.** If it isn't in the audit
   log, it didn't happen — and shouldn't have.

---

## 2. Blast radius, in detail

The `blast` field on every capability is a **promise to the user**
about the worst case. The controller uses it to decide what to confirm,
log, rate-limit, or refuse.

| Level | Examples | Reversible? | Default policy |
|-------|----------|-------------|----------------|
| `none` | `git status`, `kubectl get`, `cat` | N/A (read-only) | Auto-allow |
| `low` | `git add`, `npm install`, writing a new file | Yes, trivially | Auto-allow + log |
| `medium` | `git reset`, `rm` of created files, `truncate` | Yes, with effort | Confirm + log |
| `high` | `git push`, `aws s3 cp`, secret reads, network egress | Sometimes | Confirm + audit + 2nd-channel notice |
| `critical` | `DROP TABLE`, `rm -rf /`, prod deploys, payments, IAM changes | No | Confirm + audit + cooldown + tripled friction |

**Two-strike rule:** if an agent declares `low` and the controller
detects it ran a `high` command, the agent is quarantined pending
review. Repeated violations = removal from the org.

---

## 3. The controller is the seatbelt

The Agent-Controller is the **only** component allowed to:

- Approve or block a shell command from any agent
- Inject confirmation prompts into the user's flow
- Persist the audit log
- Hold the kill switch

No agent may execute a shell command without first calling the
controller's `command.request()`. The controller's verdict —
`allow`, `deny`, `confirm`, `dry-run-only` — is final.

This is the load-bearing rule. Most "AI agent dropped my prod table"
stories happen because the agent had a direct shell. Ours don't.

---

## 4. Sandboxing tiers

Agents declare a sandbox preference in their manifest:

| Tier | What it means | When required |
|------|---------------|---------------|
| `required` | Refuses to run outside an isolated environment (container, VM, Firecracker, WASM) | Network-touching agents, anything `high` or `critical` |
| `recommended` | Runs unsandboxed but warns | Local-only agents that touch the filesystem broadly |
| `optional` | Sandboxing not needed | Pure read-only agents |

The controller MAY upgrade the tier (force-sandbox a `recommended`
agent) but MAY NOT downgrade it. An agent's stated minimum is a
floor, not a default.

---

## 5. The kill switch

Every running agent must respond to:

- **SIGINT** → cancel current invocation, stay alive
- **SIGTERM** → finish current command, exit cleanly
- **Controller `agent.terminate`** → stop now, no further commands

The hub exposes a one-keystroke "kill all agents" path. If you can
stop everything in under a second, you can recover from anything.

---

## 6. Secret hygiene

- Agents MUST NOT log secret values. The controller redacts known
  patterns (API keys, JWTs, AWS credentials, private keys) from the
  audit log before write.
- Agents MUST NOT persist secrets to disk outside OS-managed stores
  (Keychain, Secret Service, Windows Credential Manager).
- Agents MUST NOT exfiltrate environment variables to third parties.
  An LLM-backed agent that sends `env` to its model provider without
  an explicit, per-session opt-in is non-conforming.

---

## 7. Audit log requirements

The controller writes a structured, append-only log of every action.
Format is defined in AGENT_SPEC §5.

- **Tamper-evident**: each line includes a hash chained to the prior
  line. Truncation is detectable.
- **Local first**: lives at `~/.shell-agents/audit/` by default.
- **Replayable**: any session can be replayed read-only to reconstruct
  what an agent did and why.
- **User-owned**: the user can export, inspect, and delete it.
  No telemetry by default.

---

## 8. Reporting a vulnerability

If you find a way to bypass the controller, smuggle an unlogged
command, or escalate blast radius without confirmation:

- **Do not open a public issue.**
- Use GitHub's [private vulnerability reporting](https://github.com/shell-agents/.github/security/advisories/new)
  on this repo, or email **security@shell-agents.dev** (placeholder
  until DNS lands).
- We respond within 72 hours, fix critical issues within 7 days,
  and credit you in the release notes unless you ask otherwise.

---

## 9. What we will NOT do, ever

- Ship an agent with hidden network calls
- Ship an agent that disables the controller "for performance"
- Accept a PR that removes audit logging
- Accept a PR that adds a `--yolo` flag
- Pretend our sandbox is stronger than it is

Safety claims are testable. We test them.

---

## 10. Open questions (to be resolved before v1.0)

These are deliberately unanswered today. Input welcome via RFC.

- Should `critical` capabilities require a hardware security key
  confirmation by default?
- Should the audit log be optionally signed with the user's SSH key?
- Should we ship a public "safety conformance" benchmark suite, and
  if so, against which adversarial corpus?

---

## Versioning

| Version | Status | Date |
|---------|--------|------|
| 0.1 | Draft | 2026-05-24 |
