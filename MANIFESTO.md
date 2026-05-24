# The shell-agents Manifesto

We build agents that live where work actually happens: the command line.

Not in a chat tab. Not behind an API gateway. In your terminal,
your CI runner, your SSH session, your Dockerfile, your Makefile.

This is what we believe.

---

## 1. The shell is the universal interface
Every developer, every server, every pipeline already speaks shell.
An agent that meets users there meets everyone, everywhere, today —
no SDK, no integration, no migration.

## 2. One agent, one job
A god-agent is a bad agent. We ship small agents that do one thing
sharply — `git`, `logs`, `deploy`, `triage` — and compose them.
Specialization beats sprawl.

## 3. Composable beats monolithic
Agents in our org speak a shared protocol so a hub can route, a
controller can orchestrate, and a user can swap any piece without
rewriting the others. Lego, not concrete.

## 4. Safe by construction, not by hope
An agent that can run `rm` can ruin a Friday. Every agent here ships
with explicit blast-radius scoring, dry-run defaults, and a kill
switch. Safety is a build-time property, not a runtime prayer.

## 5. Observable or it didn't happen
Every command an agent runs is logged, attributable, replayable.
If you can't audit it, you can't trust it. If you can't trust it,
you can't ship it.

## 6. Local-first, cloud-optional
Agents run on the user's machine by default. Cloud is an enhancement,
not a requirement. Your shell, your data, your call.

## 7. Open weights for the org, open contracts for everyone
Apache-2.0 across the board. Patent grant included. No rug pulls,
no relicensing surprises. The protocol is yours forever.

## 8. We say no
Not every agent belongs in this org. We will reject agents that are
unsafe, unobservable, vendor-locked, or duplicative without
improvement. A small, sharp catalog beats a bloated one.

---

## What we don't build
- Chat UIs pretending to be agents
- Agents that can't be inspected, paused, or killed
- Closed protocols dressed in open-source clothing
- "AI" wrappers around `curl`

---

## What success looks like
A developer drops a single binary onto a fresh machine and has, in
under a minute, a fleet of small specialist agents safely doing real
work in their shell — observable, swappable, and theirs.
