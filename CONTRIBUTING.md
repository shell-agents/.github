# Contributing to shell-agents

You want to ship code under this org. Good. Here's how.

This is a **product org**, not a free-for-all. We optimize for a
small, sharp catalog of agents that all behave the same way, are all
safe by construction, and all earn their keep. That bar applies to
core repos and to community agents alike.

> Read [MANIFESTO.md](./MANIFESTO.md), [AGENT_SPEC.md](./AGENT_SPEC.md),
> and [SAFETY.md](./SAFETY.md) before opening a PR. We reference
> them in reviews and we will close PRs that ignore them.

---

## TL;DR

1. Open an **issue first** for anything bigger than a typo.
2. Branch off `main`, name it `<type>/<slug>` (e.g., `feat/git-recover-undo`).
3. Sign your commits (DCO — `git commit -s`).
4. Pass the conformance suite when it lands; until then, pass tests + lint.
5. PR into `main`. One reviewer (the BDFL or a delegated maintainer) merges.
6. Be kind. We're all on call somewhere.

---

## What we accept

| Type | Where it goes | Bar |
|------|---------------|-----|
| **Bug fix** to existing agent | Same repo | Tests + reviewer approval |
| **Feature** in existing agent | Same repo | Issue first → design note → PR |
| **New capability** in existing agent | Same repo | Manifest update + spec conformance |
| **New agent** in the org | New repo (after acceptance) | Agent Proposal (§4) |
| **Spec / safety / governance change** | `.github` repo | RFC (§5) |

---

## How to propose a NEW AGENT

This is the path most contributors care about. Don't open a 5,000-line
PR cold. Do this:

### Step 1 — Open an Agent Proposal issue

Use the `Agent Proposal` issue template in the `.github` repo. It asks:

- **Name** (lowercase, dot-separated, capability-first — see SPEC §7)
- **One-line pitch** ("recovers lost git state from reflog")
- **The pain** — what user, what scenario, what's the dollar/hour cost today?
- **Why a separate agent** instead of a capability in an existing one?
- **Capabilities** you'll ship at v0.1, with declared blast radius
- **Sandbox tier** you intend to require
- **Maintainer commitment** — who keeps it alive for the next 12 months?

### Step 2 — Acceptance review

The BDFL (or a delegated maintainer) responds within 7 days with one of:

- **Accepted** — go build it in your fork, request transfer when v0.1 ships.
- **Refine** — specific changes needed, come back.
- **Merge into existing agent** — your idea is great but belongs as a capability in `shell-agents/foo` instead.
- **Declined** — we explain why. Common reasons: out of scope, unsafe, duplicates an existing agent without clear improvement.

### Step 3 — Build it in your own repo

Hit v0.1 of the AGENT_SPEC. Pass tests. Real README. Real LICENSE
(Apache-2.0). Real `agent.toml`.

### Step 4 — Request transfer

Open a transfer request issue. We:
1. Re-verify spec conformance.
2. Run the safety review checklist.
3. Transfer the repo into `shell-agents/`.
4. Add you as the maintainer of that repo.

You keep ownership of your agent. The org gives it a home.

---

## How to propose a SPEC OR SAFETY CHANGE (RFC)

Bigger leverage, higher bar. Open a PR to `.github/rfcs/NNNN-title.md`
using the RFC template. RFCs cover:

- Changes to AGENT_SPEC, SAFETY, GOVERNANCE
- New required protocol methods
- New blast levels or sandbox tiers
- Anything that affects every agent in the org

RFC lifecycle: **Draft → Review (≥7 days) → Decision (BDFL) → Merged or Closed**.
We document the *why* of every accepted RFC. Future-us will thank us.

---

## Code, branch, commit conventions

### Branches
```
feat/<slug>     new feature
fix/<slug>      bug fix
docs/<slug>     docs only
spec/<slug>     spec change
chore/<slug>    tooling, deps, CI
```

### Commits
Conventional Commits (`feat:`, `fix:`, `docs:`, `chore:`, `spec:`, `refactor:`).
Subject ≤ 72 chars. Body explains *why*, not *what*.

DCO required: `git commit -s` adds the sign-off line. PRs without
sign-off won't merge.

### Code style
Each repo specifies its own (see its `CONTRIBUTING.md` if it has one).
Default: whatever `gofmt`/`prettier`/`ruff`/`rustfmt` says. We don't
bikeshed.

---

## What we will close fast

- PRs that bypass the controller for "performance"
- PRs that add a `--yolo`, `--force-everything`, `--skip-confirmation` flag
- PRs that lower a capability's declared blast radius without a real reason
- PRs that disable audit logging
- PRs that add network calls without declaring them
- PRs that vendor an LLM provider's SDK into a "vendor-neutral" agent without justification
- "AI-generated" PRs with no human reviewer attached

Closing a PR isn't an insult. It's how we keep the catalog clean.

---

## Maintainer expectations

If you become a maintainer of a `shell-agents` repo, you commit to:

- **Triage within 7 days** — at least acknowledge new issues
- **Keep the manifest honest** — update blast radius and capabilities as the agent evolves
- **Ship a security fix within 7 days** of a confirmed vuln
- **Hand off cleanly** — if you can't maintain anymore, file a stewardship handoff issue. The org will find a new home or archive gracefully.

We'd rather archive a repo than let it rot publicly.

---

## Code of Conduct

Standard Contributor Covenant applies (see CODE_OF_CONDUCT.md when
that lands). Short version: **be kind, be specific, attack ideas not
people**. Repeat offenders lose access. We will not negotiate this.

---

## License & DCO

- All code: **Apache-2.0**.
- All commits: **DCO sign-off** (`Signed-off-by: ...` line via `git commit -s`).
- By contributing, you agree your contribution is yours to license and
  is offered under Apache-2.0.

We do not require a CLA. The DCO is enough.

---

## Getting help

- **Quick question?** Open a Discussion in the relevant repo.
- **Bug?** Open an issue with reproducer + agent version + manifest.
- **Security issue?** Don't open an issue — see [SAFETY.md §8](./SAFETY.md).
- **Want to chat?** (Discord/Matrix link TBD.)

---

## A note on AI-assisted contributions

Most of you will write PRs with help from an agent. That's fine — we
do too. Two rules:

1. **You** are the author. You read the diff, you understand it, you
   defend it in review. "The AI wrote it" is not a defense.
2. **Don't auto-spam.** Bulk-generated low-quality PRs get the author
   blocked from the org. We're building agents that respect users;
   that respect runs in both directions.

---

## Versioning

| Version | Status | Date |
|---------|--------|------|
| 0.1 | Draft | 2026-05-24 |
