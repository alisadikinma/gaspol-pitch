---
name: pitch-review
description: The differentiator phase of gaspol-pitch. Use to adversarially review a pitch deck like a skeptical investor before it is sent — binary rubric linting, an 11-dimension scored rubric (/55), hard unit-economics thresholds, cross-slide consistency, an earned-secret gate, and a tough-question Q&A simulation. Emits review.md with a BLOCKING verdict that loops back to pitch-draft until the deck passes. Works on any deck.md (ours or the user's own).
---

# pitch-review (adversarial investor judge)

> The load-bearing phase. It does NOT trust the draft's self-report. It scores the
> deck like a partner who wants a reason to say no, then blocks until the deck is
> fundable.

**Announce:** "Running pitch-review — scoring the deck like a skeptical investor. It will block if the deck isn't ready."

## Input

`deck.md` (from pitch-draft, OR a deck the user brings) + `discovery.md` (for the
target profile + stage, if present).

## Load (the brain)

- `../../references/investor-deck-rubric.md` — binary checks (PART B/C/D).
- `../../references/vc-review-rubric.md` — scored judgment (/55 + ABC + conviction + consistency + earned-secret + narrative gates).
- `../../references/unit-economics.md` — hard thresholds.
- `../../references/vc-fundamentals.md` — DD checklist + objection list.

## Procedure (run all layers, in order)

### (a) Binary lint — 9 PART-C checks per slide
For EVERY slide, pass/fail each: **Value-Anchor · Pyramid · Assertion-vs-Topic ·
One-Message · Linguistic-Fluff · 5-Second · Data-Parsing · Platform-Risk ·
Forwardable.** Any fail is logged with the slide and the exact offending text.

### (b) Scored judgment
Apply `vc-review-rubric.md`: the **11-dimension rubric scored 1–5 (=/55)** with a
per-dimension fix for anything < 3; the **ABC dual-test** per slide (reduces risk? /
increases outcome value? / cut); the **4 Conviction Layers** coverage
(Market/Insight/Founder/Execution — Covered/Thin/Missing).

### (c) Signal thresholds
Check PART-B signals against `unit-economics.md` hard gates: **LTV ≥ 3× CAC**,
**CAC payback < 12 mo**, **bottom-up TAM**, venture-scale growth (≥ ~2× YoY / 2×
QoQ where claimed), and the model-appropriate metric (NRR for SaaS, take-rate+GMV
for marketplace, SW-attach for hardware, inference-COGS for AI).

### (d) Cross-slide consistency
Reconcile across slides: **TAM ↔ traction ↔ ask**, price ↔ unit-econ, AI-COGS
sanity, headcount ↔ use-of-funds, story continuity. Flag any number that doesn't
reconcile (no single-slide check catches these).

### (e) Earned-secret gate + narrative gates
Require **≥ 1 earned secret** across tech/market/GTM (earned-vs-faked test). Run
**30-second retell** + **dinner test**. Run the `vc-fundamentals.md` objection list:
for each common objection, is the deck's answer present and convincing?

### (f) Skeptic subagent — Q&A simulation
Spawn a fresh subagent prompted as a skeptical partner: "Assume this deck has
problems. For each slide, write the toughest question you'd ask, then name the 3
weakest claims in the whole deck." Also run the **Forwardable Test** (would the
narrative stand alone with zero verbal context?). Fold its output into the report.

## Output — `review.md`

```
## Binary lint (per slide)
| slide | the 9 checks | fails (with quoted offending text) |

## Score: NN/55 — grade <A–F>
| dimension | score | fix if <3 |
ABC verdict per slide · Conviction layers (4 rows) coverage

## Signals
LTV:CAC, payback, TAM type, growth, model-metric — pass/fail vs threshold

## Consistency
unreconciled numbers (or "all reconcile")

## Earned secret · narrative gates
count + earned-vs-faked · 30s-retell pass/fail · dinner-test line

## Q&A simulation
toughest question per slide · the 3 weakest claims · Forwardable: y/n

## VERDICT: PASS | BLOCKING
(if BLOCKING) exact fix list → return to pitch-draft
```

## BLOCKING verdict + gate-loop

Return **BLOCKING** if ANY of: a binary check fails · score < 38 · any single
dimension = 1 · an unreconciled cross-slide inconsistency · 0 earned secrets · a
hard unit-econ gate missed with no credible path. On BLOCKING, route back to
**pitch-draft** with the exact fix list and re-review. Only **PASS** unlocks
pitch-finish. Do not soften a verdict to be polite — that defeats the plugin.

## Self-test (this skill's correctness contract)

- `../../references/examples/bad-deck.md` MUST be caught failing: **Linguistic-Fluff**
  ("world-class", "synergy", "best-in-class", "cutting-edge", "revolutionize",
  "paradigm-shifting"), **Assertion-vs-Topic** ("Market Size", "Traction", "The Ask"),
  **One-Message** (slide 3's triple "and"), **Data-Parsing** ("four million"),
  top-down **TAM**, and the **Ask** fail ("raising $2M to grow"). Verdict = BLOCKING.
- `../../references/examples/good-deck.md` (Acme) MUST PASS: assertion headlines,
  bottom-up TAM ($4B = 888 × $4,800), 3.6× LTV:CAC / 7-mo payback, exact ask with
  use-of-funds split. Verdict = PASS.
- pitch-review is correct only if it FAILS the bad fixture and PASSES the good one.
