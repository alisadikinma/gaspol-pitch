---
name: pitch-narrative
description: Second phase of gaspol-pitch. Use after pitch-discovery to choose the slide sequence and write the thesis spine — the ordered headline assertions that alone reconstruct the investment thesis. Picks from the validated 14-slide spine (traction-first when metrics are exceptional), grounds the arc in Khosla (emotional) + Sequoia (systematic), and reuses storybrand + made-to-stick. Outputs narrative-plan.md.
---

# pitch-narrative

> Decide the order and the headlines *before* writing slide bodies. If the
> headlines alone don't tell the story, no amount of bullet polish saves the deck.

**Announce:** "Running pitch-narrative — choosing the spine and writing the thesis headlines."

## Input

`discovery.md` (facts + target profile + gap list).

## Reuses (installed — call, don't recreate)

- **storybrand** — cast the customer as the hero, the product as the guide; clarify the stakes.
- **made-to-stick** — make each headline concrete, credible, and memorable (SUCCESs).
- **influence** — sequencing for commitment/consistency where it helps.
- Reads `../../references/deck-narrative.md` (spine + templates + grounding) and `../../references/hormozi-offer.md` (Value Equation per slide).

## Step 1 — pick the spine

Default to the **validated 14-slide spine** in `references/deck-narrative.md`
(Title · Problem · Solution · Why-now · Market · Product · Traction · Business-model
· Competition · GTM · Team · Financials · Ask · Vision).

- **Traction-first rule:** if early metrics are exceptional (real revenue, compound
  growth, marquee logos), move Traction to slide 2–3.
- Add any **required deck block** the runtime target profile demands (read from
  discovery.md) — e.g. an alignment/contribution slide. Generic: read it, don't
  hardcode any specific accelerator's block.
- Cut to ≤ 12–15 slides.

## Step 2 — write the headline thesis spine

For each slide, write a **complete assertion sentence** (not a label), max 2 lines,
insight front-loaded. The set must pass the **spine test**: reading only the
headlines reconstructs the full thesis.

- ✅ "Market is a $4B bottom-up opportunity" · ❌ "Market Size"
- ✅ "Manual inspection misses 1 in 12 defects" · ❌ "The Problem"

## Step 3 — ground the arc

- **Khosla (emotional):** pick what to lead with so the investor *feels* the stakes and inevitability.
- **Sequoia (systematic):** ensure every emotional beat is backed by a number or a demo named in the spine.
- For each slide, note which **Value-Equation** variable it moves (Dream Outcome ↑, Likelihood ↑, Time Delay ↓, Effort ↓) — from `hormozi-offer.md`.

## Output — `narrative-plan.md`

```
## Spine (ordered)
NN. <slide type> — "<headline assertion>"  [moves: <value-eq variable>]  [evidence: <number/demo>]
...
## Notes
- traction-first? y/n + why
- target-required blocks included: <list>
- the one dinner-test line (the vision the partner repeats)
```

Hand off to **pitch-draft**.
