# shell-agents Roadmap

What we're building, in what order, and what we're explicitly **not**
building yet. Honest about uncertainty: dates are intentions, not
promises.

> Status: living document. Changes go through an [RFC](./CONTRIBUTING.md#how-to-propose-a-spec-or-safety-change-rfc).
> Last updated: 2026-05-24

---

## North Star

> A developer drops a single binary onto a fresh machine and has, in
> under a minute, a fleet of small specialist agents safely doing real
> work in their shell — observable, swappable, and theirs.

Every phase below is judged against that one sentence.

---

## Phase 0 — Foundation *(now)*

**Goal:** the org exists, the rules are clear, the spec is real on paper.

- [x] MANIFESTO v0.1
- [x] AGENT_SPEC v0.1
- [x] SAFETY v0.1
- [x] CONTRIBUTING v0.1
- [x] Org profile README
- [x] ROADMAP v0.1 *(this doc)*
- [ ] GOVERNANCE v0.1
- [ ] CODE_OF_CONDUCT
- [ ] SECURITY.md (vuln disclosure)
- [x] GitHub issue / PR / RFC templates

**Exit criteria:** a stranger can land on the org, read for 10 minutes,
and know exactly what we're building, what we won't accept, and how to
contribute.

---

## Phase 1 — Prove the spec *(next, ~4–6 weeks)*

**Goal:** turn the paper spec into running code.

- [x] **`agent-template`** — minimal spec-conformant agent skeleton.
      Implements `describe`, `invoke`, `cancel`, `health`. Ships with
      a Makefile, tests, GitHub Actions, and an `agent.toml`.
- [ ] **`spec-conformance`** — a test harness that black-box-validates
      any binary against AGENT_SPEC. Pass = badge.
- [ ] **`agent-toml`** — a TOML schema + validator library. Used by
      template, conformance, and (later) the controller.

**Exit criteria:** anyone can clone `agent-template`, change five
lines, and have a spec-conformant agent that passes conformance.

---

## Phase 2 — The runtime *(6–10 weeks after Phase 1)*

**Goal:** ship the controller and hub, end-to-end, on one platform first.

- [ ] **`controller`** — JSON-RPC dispatcher, blast-radius enforcement,
      hash-chained audit log, kill switch. Linux + macOS, Windows later.
- [ ] **`hub`** — TUI front-end. One keystroke kill-all. Session history.
      Inline confirmation flow for medium/high/critical actions.
- [ ] **`git-recover`** — flagship reference agent. Reads reflog,
      proposes recovery, executes on confirm. Blast: `medium`.

**Exit criteria:** `curl | sh → hub → "I lost my last 3 commits" →
agent runs → commits restored → audit log readable`. End to end. Real.

---

## Phase 3 — Catalog *(after Phase 2 is stable)*

**Goal:** prove the model with breadth.

Likely first agents (in order of probable usefulness):

- [ ] `logs.tail` — smart log tailing across k8s / systemd / files
- [ ] `aws.audit` — read-only AWS sanity check (blast: `none`)
- [ ] `db.diagnose` — read-only DB health (blast: `none`)
- [ ] `deploy.runbook` — learns from your terminal during incidents

We will *not* commit to all four. We pick based on:
1. Real maintainer signing up
2. No existing agent in the org covers it
3. Pain is high, blast is honest

**Exit criteria:** ≥3 third-party agents accepted into the org through
the proposal flow. Catalog is no longer just "us."

---

## Phase 4 — Hardening *(parallel to Phase 3)*

**Goal:** earn the "safe by construction" claim.

- [ ] Sandboxing reference implementations (container, Firecracker, WASM)
- [ ] Adversarial test corpus for the conformance suite
- [ ] Signed audit logs (optional, user's SSH key)
- [ ] Public security report cadence (quarterly)
- [ ] First external security review (paid or community)

**Exit criteria:** an enterprise security team can read SAFETY.md +
our latest report and say "yes, fine."

---

## Phase 5 — Beyond *(speculative, ≥ Q1 2027)*

Things we *might* build if the foundation holds:

- A registry / discovery service for community agents (carefully — registries are governance landmines)
- IDE adapters (VS Code, JetBrains, Zed) over the same protocol
- A managed cloud offering for teams who want shared audit + policy
- Cross-agent memory protocol (today, agents are stateless between invocations)

These are deliberately vague. We earn the right to design them by
shipping Phases 1–4 well.

---

## Things we are NOT doing

This list is as load-bearing as the active list.

- A web chat UI. We are terminal-native. Other people can build wrappers.
- A model provider. We don't train, host, or proxy LLMs.
- A vendor lock-in. No agent in this org may require a specific LLM provider.
- A plugin marketplace with paid listings. The catalog is curated, not commercial.
- "Agentic" hype features without a concrete user story. The shell is the bar.

---

## How decisions get made

- Roadmap changes: BDFL decides, after RFC review.
- Phase order can shift if a Phase 2 prerequisite is blocked.
- Phase 5 items move to Phase 4 only via RFC.
- Scope creep within a phase is the BDFL's job to refuse.

---

## Calendar (best-effort)

| Phase | Target start | Target end | Confidence |
|-------|--------------|------------|------------|
| 0 | 2026-05-24 | 2026-06 | in flight |
| 1 | 2026-06 | 2026-07 | likely |
| 2 | 2026-07 | 2026-09 | likely |
| 3 | 2026-09 | open | directional |
| 4 | 2026-09 | open | directional |
| 5 | 2027+ | open | speculative |

We will update this monthly. We will not pretend dates are firm when
they're not.
