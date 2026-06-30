---
name: pitch-discovery
description: First phase of gaspol-pitch. Use to gather and pressure-test the facts a pitch deck needs before any slide is written — company, problem, solution, traction, team, ask, and the user's target VC/accelerator profile. Runs a probing-question contract, pressure-tests the problem with mom-test + lean-startup, and produces a stage-scoped gap list. Outputs discovery.md.
---

# pitch-discovery

> Garbage in → garbage deck. This phase extracts honest, specific, quantified
> facts and flags what's missing *for the raise stage* before drafting starts.

**Announce:** "Running pitch-discovery — gathering and pressure-testing the facts."

## Reuses (installed — call, don't recreate)

- **mom-test** — pressure-test that the problem is real (talk about their life, not your idea; past behavior over hypotheticals).
- **lean-startup** — frame riskiest assumptions + what evidence would validate them.
- Reads `../../references/vc-fundamentals.md` (stage map) and `../../references/business-model.md` (model questions).

## Step 1 — the discovery contract (probing question bank)

Collect answers to a `narrative.md`-style MUST-HAVE intake. Ask the sharp version
of each — push past the first vague answer:

**Problem**
- Who *specifically* experiences this? (a named segment, not "businesses")
- How painful is it — **quantify** (cost, time, frequency, $ lost)?
- What do they do today instead? Why isn't that working?

**Solution / insight**
- What's the "aha" — and why is your outcome **10×**, not 10%?
- What do you understand that competitors don't (the earned secret)?

**Why now**
- What recent shift (tech / regulation / behavior, last 1–3 yrs) makes this possible *now*?

**Moat**
- Why is this **hard to replicate** in 12 months? (data, network, switching cost)

**Traction**
- Real numbers: revenue, growth rate, retention, marquee customers. Compound, not cumulative.

**Team**
- How does each founder's past map directly to *this* problem (founder-market fit)?

**Business model + ask**
- Who pays, how much, how often? (→ business-model.md)
- Exact raise, use-of-funds split, and the milestone it unlocks?

For each, if the answer is vague or hypothetical, mark it and re-ask. Honest "we
don't have this yet" is fine and feeds the gap list.

## Step 2 — runtime target profile (NOT bundled)

The plugin ships no specific accelerator/VC. Collect the target as a runtime input,
using this **inline field schema** (read a user-provided target file if one exists,
else ask):

- **Selection criteria** — what this investor explicitly evaluates.
- **Required deck blocks** — any mandatory sections (e.g. an alignment/contribution slide).
- **Disqualifiers** — what gets an instant pass.
- **Alignment language** — how to phrase fit with their thesis/mandate.
- **Stage** — pre-seed / seed / A / B (drives the gap list).

Store these in `discovery.md`; later phases read them. Never hardcode a specific
target into the plugin.

## Step 3 — stage-scoped GAP list

Using `../../references/vc-fundamentals.md` stage map, list ONLY what *this stage's*
investors must see and the user currently lacks. Don't demand Series-A proof from a
pre-seed deck. Each gap = what's missing + how to close or honestly frame it.

## Output — `discovery.md`

```
## Facts
problem / solution / why-now / moat / traction / team / business-model / ask
(each: the sharpened, quantified answer — or "MISSING")

## Target profile (runtime)
selection criteria · required blocks · disqualifiers · alignment language · stage

## GAP list (scoped to <stage>)
- [gap] — how to close / how to frame honestly
```

Hand off to **pitch-narrative**.
