# Slide Design Canon + nano-banana-pro SLIDE prompt archetype

> Brain for `pitch-visual`. Generic principles only — **no specific brand**
> (no fixed hex, @handle, named portrait style). Brand chrome is a RUNTIME input
> the user supplies; this file never bakes one. Theming canon adopted from public
> presentation-design guidance (Anthropic pptx "Design Ideas" + McKinsey-style
> chart-discipline), summarized.

## Render policy

Each slide = **one full 16:9 nano-banana-pro image**, text and numbers rendered
in-image (nano banana pro is SOTA at in-image text). Bake exact headline + numbers
**quoted, with font/size directives**. After generation (if an MCP is present),
run the **functional render-pass** (below) to catch drift.

## Theming canon (apply to every slide for consistency)

- **60/30/10 color dominance** — 60% dominant (usually background), 30% secondary, 10% accent. Pick 2–3 curated palette pairs at deck start and reuse them; never improvise per slide.
- **Dark/light sandwich** — alternate dark and light slides to create rhythm (e.g. dark title + section dividers, light content). Keeps a long deck from going monotone.
- **Type scale (consistent across slides):** title 36–44 · section 20–24 · body 14–16 · caption 10–12. Never more than ~3 sizes on one slide.
- **Grid & spacing** — 0.5" minimum margins, consistent gutters. Align everything to a grid; ragged alignment reads as amateur.
- **ONE repeated motif** — a single shape/line/icon style carried across slides = cohesion. Not a different decoration each slide.
- **Never an accent line under the title** — the #1 AI-slop tell. Also: never a text-only slide (every slide earns a visual), never centered body paragraphs, never 5+ font sizes.

## Slide-variant taxonomy (pick the layout that fits the content)

`title` · `section` (divider) · `cards-3` (three parallel points) · `split`
(text left / visual right) · `stats` (3–4 big numbers) · `kpi-hero` (one giant
metric) · `comparison` (before/after, us/them) · `matrix` (2×2 quadrant) ·
`chart` (one data viz) · `flow` (process / pipeline) · `team` (photos + fit) ·
`ask` (raise + use-of-funds).

## The 5-section SLIDE prompt template (what pitch-visual writes per slide)

```
LAYOUT:    which variant (from taxonomy) + where each block sits on a 16:9 grid
VISUAL:    the imagery/diagram/chart — concrete, editorial/infographic (NOT a portrait)
STYLE+PALETTE: aesthetic direction + the 2–3 palette pair + the one motif + type scale
TEXT:      the EXACT headline + every number, VERBATIM in quotes, with font + size
           e.g. headline "Market is a $4B bottom-up opportunity" at 40pt bold;
                stat "3.2× LTV:CAC" at 28pt
CONSTRAINTS: 16:9 aspect, safe margins, no accent-line-under-title, legible at
             thumbnail size, brand chrome = [RUNTIME PLACEHOLDER: logo/palette/font]
```

## Per-slide-type visual recipe (quick reference)

- **Problem** → a single evocative scene of the pain, or a stat that quantifies it.
- **Solution** → product-in-action, the before→after.
- **Market** → bottom-up build (segment → ACV → TAM), not a pie of "$X trillion".
- **Traction** → an up-and-to-the-right line/bar with the real numbers labeled.
- **Competition** → 2×2 matrix; you in the winning quadrant, axes you actually own.
- **Business model** → flow of who-pays-whom.
- **Team** → photos + one-line founder-market-fit each.
- **Ask** → the raise as one number + a use-of-funds split + the milestone it unlocks.

## Data-viz chart choice

- Trend over time → line. · Compare categories → bar. · Part-of-whole → ONE pie max (or stacked bar). · Two variables → scatter/quadrant. · Funnel/process → flow.
- One chart per slide, one message per chart, label the point you want made.

## WOW-score gate + anti-AI-look

Before accepting a prompt, score it: does it look like a deck a top firm would
fund, or a generic template? Reject generic stock-photo vibes, accent-line tells,
clip-art icons, and centered walls of text. Aim for editorial confidence.

## Functional render-pass spec (run if a PNG was generated)

A fresh subagent inspects each rendered PNG with the instruction **"assume there
are issues — find them"**, checking:
- text overflow / clipping off the safe margin,
- garbled or misspelled text,
- a **wrong or illegible number** (compare against the deck's source number),
- layout broken vs the requested variant.

On any defect → regenerate that slide (max 2 retries), then flag for manual fix if
still broken. `ponytail:` if numbers keep drifting on a slide, the upgrade path is
a hybrid real-text overlay (render the visual, set the numbers as real text) — not
built in v0.1.

## Hard rule

Brand chrome (logo, exact palette, font, @handle) is **always** a runtime input.
This file ships zero specific brand identity. CI guard greps `references/` for any
leaked brand token.
