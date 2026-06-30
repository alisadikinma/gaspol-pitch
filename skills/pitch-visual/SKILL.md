---
name: pitch-visual
description: Fifth phase of gaspol-pitch, run only after pitch-review PASSES. Use to author one nano-banana-pro image prompt per slide (16:9 editorial/infographic) from the deck-design canon, reusing carousel-gen as the prompt SSOT and gaspol-design for palette/font/chart. Bakes each slide's exact headline + numbers into the prompt verbatim. If an image MCP is present it renders PNGs and runs a functional QA pass; if not, it emits prompts for manual rendering. Image MCP is optional.
---

# pitch-visual

> One full-slide image per slide, text and numbers in-image. The prompt is the
> deliverable; rendering is an optional, separable step.

**Announce:** "Running pitch-visual — authoring one nano-banana-pro prompt per slide."

## Precondition

`review.md` verdict = **PASS**. Do not render a deck that hasn't passed review.

## Reuses (installed — call, don't recreate)

- **carousel-gen** (`ai-image-carousel-prompt-gen`) — the SSOT prompt machinery ("author prompt, consumer fulfills").
- **gaspol-design** — palette / font / chart choice for the slide.
- Reads `../../references/deck-design.md` (slide archetype + 5-section template + theming canon + variant taxonomy + functional-render-pass spec).

## Step 1 — author a prompt per slide

For EACH slide in `deck.md`, write `prompts/slide-NN.md` using the **5-section
SLIDE template** from `deck-design.md`:

```
LAYOUT:    variant (title/stats/matrix/chart/...) + grid placement
VISUAL:    editorial/infographic imagery or diagram (NOT a cinematic portrait)
STYLE+PALETTE: aesthetic direction + the 2–3 palette pair + the one motif + type scale
TEXT:      the slide's EXACT headline + every number, VERBATIM in quotes, with font+size
           e.g. headline "Market is a $4B bottom-up opportunity" at 40pt bold
CONSTRAINTS: 16:9, safe margins, no accent-line-under-title, legible at thumbnail,
             brand chrome = [RUNTIME PLACEHOLDER: logo/palette/font from user]
```

Apply the theming canon (60/30/10, dark/light sandwich, type scale, one motif) and
pick the variant per `deck-design.md`'s per-slide-type recipe. **Brand chrome is a
runtime placeholder — never bake a specific logo/hex/font/handle.**

## Step 2 — MCP-optional fulfillment

Detect an image MCP (`indusia-image-gen` or `higgsfield`; confirm a nano-banana-pro
model id via `list_image_models` / `models_explore`).

- **MCP present** → generate `slide-NN.png` for each prompt, then run the
  **functional render-pass**: a fresh subagent inspects each PNG with "assume there
  are issues — find them" (overflow, clipping, garbled text, **wrong/illegible
  number** vs the deck source, broken layout). On any defect → regenerate that slide
  (max 2 retries), then flag for manual fix if still broken.
- **MCP absent** → write a one-line note at the top of `prompts/` ("no image MCP —
  paste each prompt into nano banana pro manually") and stop after the prompts. This
  is NOT a failure — the prompts are the deliverable.

## Output

`prompts/slide-NN.md` (one per slide) + optional `slide-NN.png`. Hand off to
**pitch-finish**.

`ponytail:` if numbers keep drifting on a number-heavy slide, the upgrade path is a
hybrid real-text overlay (render visual, set numbers as real text) — not built in v0.1.
