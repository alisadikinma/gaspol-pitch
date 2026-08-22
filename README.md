# gaspol-pitch

> Build an investor/accelerator pitch deck the way `gaspol-dev` builds software:
> **discovery → narrative → draft → adversarial review → visual → finish.**
> Reuses your already-installed marketing skills as the engine. The killer
> feature is an **adversarial investor-review linter** that scores the deck like
> a skeptical VC and *blocks* it from finishing until it passes.

## Install

```bash
claude plugin marketplace add alisadikinma/gaspol-one
claude plugin install gaspol-pitch@gaspol-one
```

The [gaspol-one](https://github.com/alisadikinma/gaspol-one) marketplace also carries
`gaspol-dev` (execution fidelity) and `gaspol-catalog` (B2B sales decks).

## What it does

A multi-skill orchestrator. You give it your company facts (and optionally a
target VC/accelerator profile); it produces:

1. **`deck.md`** — slide-by-slide copy + speaker notes (Marp markdown, the source of truth).
2. **`prompts/slide-NN.md`** — one **nano-banana-pro image prompt per slide** (editorial/infographic). Paste each into nano banana pro to render the slide.
3. **`review.md`** — the adversarial investor-linter report (binary checks + scored /55 + tough-question simulation + a BLOCKING verdict).
4. *(optional)* `slide-NN.png` — auto-rendered if an image MCP is present, else you run the prompts manually. **No image MCP is required.**

## Why it's different

Most "make a deck" tools stop at generation. This one does not trust the draft.
After drafting, `pitch-review` runs a skeptic-investor pass — binary rubric
linting, an 11-dimension scored rubric, hard unit-economics thresholds
(LTV ≥ 3× CAC, CAC payback < 12 mo, bottom-up TAM), cross-slide consistency
(your TAM, traction, and ask must reconcile), an earned-secret gate, and a
toughest-questions Q&A simulation. Any binary fail or sub-threshold score loops
back to the draft with an exact fix list. You ship a deck that already survived
a skeptic.

## Quickstart

```
Use gaspol-pitch to build my seed deck.
```

The orchestrator detects what you already have and enters at the right phase. It
will not finish a deck that has not passed `pitch-review`.

## Pipeline

| # | Skill | Job |
|---|-------|-----|
| 0 | `gaspol-pitch` | Orchestrate; enforce the review gate |
| 1 | `pitch-discovery` | Collect facts via a probing-question contract; scope the gap list to your raise stage |
| 2 | `pitch-narrative` | Pick the slide sequence (validated 14-slide spine) + write the thesis headlines |
| 3 | `pitch-draft` | Write the Marp slides + speaker notes |
| 4 | `pitch-review` | **Adversarial investor judge** (binary + scored + Q&A-sim + BLOCKING verdict) |
| 5 | `pitch-visual` | One nano-banana-pro prompt per slide (+ optional render with a functional QA pass) |
| 6 | `pitch-finish` | Gate on review PASS; optional multi-reviewer polish; bundle the deliverable |

## The brain (`references/`)

Bundled, **generic only** (no single-company/single-event knowledge): the investor
rubric, a scored VC-review rubric, Hormozi offer methodology, deck-narrative
canon (+ validated 14-slide spine), business-model canvas, unit-economics
thresholds, VC fundamentals, and the slide-design canon (theming + the
nano-banana-pro slide-prompt archetype). Skills stay thin and read these; they
never hardcode domain knowledge in prose.

Your target VC/accelerator profile, your company, and your brand chrome are
**runtime inputs**, never bundled.

## Further reading (not bundled — too large)

- [joelparkerhenderson/pitch-deck](https://github.com/joelparkerhenderson/pitch-deck) — pitch-deck advice corpus
- [midovislam/awesome-pitch-decks](https://github.com/midovislam/awesome-pitch-decks) — collection of real decks

## Status

v0.1.0 — first cut. Editable `.pptx` export (pptxgenjs) is a deferred upgrade;
today the render path is full-slide nano-banana-pro images.

## License

MIT — see [LICENSE](LICENSE). Adopted frameworks are credited per-file in `references/`.
