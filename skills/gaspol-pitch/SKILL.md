---
name: gaspol-pitch
description: Orchestrate investor/accelerator pitch-deck creation end to end. Use when the user wants to build, write, improve, or review a pitch deck, investor deck, fundraising deck, seed/Series-A deck, accelerator application deck, or says "buatkan deck"/"bikin pitch deck". Routes through discovery -> narrative -> draft -> adversarial investor review -> visual -> finish, reusing installed marketing skills and a bundled rubric brain. Enforces a blocking review gate before any deck is finished.
---

# gaspol-pitch (orchestrator)

> Build a deck VCs actually fund, as a repeatable pipeline — and never ship one
> that hasn't survived a skeptical-investor review.

**Announce at start:**
> "I'm using gaspol-pitch to orchestrate your deck. It runs discovery → narrative → draft → adversarial review → visual → finish, and will not finish a deck that hasn't passed pitch-review."

## Core principle (read `../../CLAUDE.md`)

Every phase skill is **thin**: it calls an already-installed marketing skill for
domain knowledge and reads the bundled `references/` brain. The orchestrator does
not do the work — it routes to the phase skills in order and **enforces the review
gate-loop**. Never hardcode domain knowledge here; it lives in `references/`.

## The pipeline

| # | Skill | Job (ONE) | Reuses (installed) | Output |
|---|-------|-----------|--------------------|--------|
| 1 | `pitch-discovery` | Collect facts via a probing-question contract + runtime target profile; scope the gap list to the raise stage | mom-test, lean-startup | `discovery.md` |
| 2 | `pitch-narrative` | Pick the slide sequence (14-slide spine, traction-first if warranted) + write the thesis headlines | storybrand, made-to-stick, influence | `narrative-plan.md` |
| 3 | `pitch-draft` | Write the Marp slides + speaker notes, one job per slide | obviously-awesome, 100m-offers, monetizing-innovation, traction, blue-ocean-strategy | `deck.md` |
| 4 | `pitch-review` | **Adversarial investor judge** — binary + scored /55 + Q&A-sim + BLOCKING verdict | (the new logic) | `review.md` |
| 5 | `pitch-visual` | One nano-banana-pro prompt per slide (+ optional render + functional QA pass) | carousel-gen, gaspol-design | `prompts/slide-NN.md` (+ optional PNG) |
| 6 | `pitch-finish` | Gate on review PASS; optional multi-reviewer polish; bundle the deliverable | gaspol-knowledge, obsidian | final bundle |

## Routing (enter at the right phase)

Detect what the user already has and skip ahead — don't redo finished work:

- No facts gathered yet → start at **pitch-discovery**.
- Facts exist, no narrative → **pitch-narrative**.
- Narrative exists, no slides → **pitch-draft**.
- A `deck.md` exists (theirs or ours) and they want it checked → **pitch-review**.
- Review PASSED, they want visuals → **pitch-visual**.
- Visuals/prompts done, ready to ship → **pitch-finish**.

When unsure which artifacts exist, ask once, then enter at the right phase.

## HARD GATE (non-negotiable)

```
pitch-review MUST run and return PASS before pitch-finish.
A deck that has not passed the review verdict cannot be finished.
```

The gate-loop: if `pitch-review` returns BLOCKING (any binary fail, score < 38,
unreconciled cross-slide inconsistency, or 0 earned secrets), route back to
**pitch-draft** with the exact fix list, redraft, and re-review. Repeat until PASS.
This loop is the whole point of the plugin — do not bypass it "to save time".

## Runtime inputs (never bundled — the user supplies)

- **Target profile** — the specific VC/accelerator (selection criteria, required deck blocks, disqualifiers, alignment language). `pitch-discovery` defines the field schema inline and reads a user-provided target file if one exists.
- **Company facts** — gathered in discovery.
- **Brand chrome** — logo / palette / font / handle, applied by `pitch-visual` as a placeholder. The plugin ships no brand.

## References index (the brain)

`references/investor-deck-rubric.md` · `vc-review-rubric.md` · `hormozi-offer.md` ·
`deck-narrative.md` · `business-model.md` · `unit-economics.md` ·
`vc-fundamentals.md` · `deck-design.md` · `examples/good-deck.md` ·
`examples/bad-deck.md`. Each phase skill reads the ones it needs (see `../../CLAUDE.md` reuse map).

## If an installed skill is missing

If a skill named in the reuse map isn't installed at runtime, STOP and tell the
user — do not fabricate a substitute or inline the knowledge.
