---
name: pitch-draft
description: Third phase of gaspol-pitch. Use after pitch-narrative to write the actual slides — Marp markdown (one slide per ---) plus speaker notes, one job per slide, contextualized numbers, <=15 slides. Reuses obviously-awesome, 100m-offers, monetizing-innovation, traction, and blue-ocean-strategy, and reads the business-model + unit-economics references. Outputs deck.md.
---

# pitch-draft

> Turn the headline spine into slides a VC can scan in 30 seconds. Every slide does
> ONE job; every number is contextualized.

**Announce:** "Running pitch-draft — writing the Marp deck + speaker notes."

## Input

`narrative-plan.md` (the ordered headline spine) + `discovery.md` (facts + any
target-required blocks).

## Reuses (installed — call, don't recreate)

- **obviously-awesome** — the positioning headline + category framing.
- **100m-offers** — the offer/value slide (apply the Value Equation from `hormozi-offer.md`).
- **monetizing-innovation** — pricing + willingness-to-pay for the model slide.
- **traction** — the channels/growth framing for the traction + GTM slides.
- **blue-ocean-strategy** — the competition slide (the axis you own, not a feature war).
- Reads `../../references/business-model.md` + `../../references/unit-economics.md` for the model + financials slides, and `../../references/investor-deck-rubric.md` PART A (one job per slide) + PART D (bullet discipline).

## Output format — Marp `deck.md`

- One slide per `---` separator.
- Slide body: the headline (assertion, from the spine) + ≤ 5 bullets.
- Speaker notes as `<!-- notes: ... -->` after each slide.

```
---

# <headline assertion from the spine>

- <assertion → evidence bullet>
- <assertion → evidence bullet>

<!-- notes: what to say out loud; the source numbers -->
```

## Rules (enforced — pitch-review will check these)

- **One job per slide** (rubric PART A). If a slide needs "and" to describe it, split it.
- **Assertion headlines**, never topic labels ("Market is a $4B opportunity", not "Market Size").
- **Bullet discipline** (PART D): ≤ 5 bullets/slide, < 40 words each, active voice, each = assertion → evidence.
- **Contextualized numbers**: never a floating figure — benchmark it ("3.6× LTV:CAC vs the 3× bar").
- **No VC-slop**: no synergy / world-class / paradigm-shift / revolutionize / cutting-edge / best-in-class.
- **Data formatting**: "$2M", "15%", "4.1×" — never "four million".
- **≤ 12–15 slides** total.
- Cover any **target-required deck block** from discovery.md (generic — read it, don't hardcode a specific accelerator's requirement).
- Business-model + financials slides must be **consistent** (price ↔ unit econ) per `unit-economics.md` — pitch-review reconciles these.

## Hand off

Emit `deck.md` and route to **pitch-review** (mandatory before any finish).
