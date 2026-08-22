# gaspol-pitch — plugin rules (for Claude)

This plugin orchestrates investor pitch-deck creation. It mirrors the gaspol-dev
pattern but the domain is **decks, not code**. Read this before touching any skill.

## Core principle — reuse, don't recreate (ponytail)

Every phase skill is **THIN**. It calls the relevant already-installed marketing
skill for domain knowledge and only adds orchestration glue + the rubric. The ONE
genuinely new piece of logic is `pitch-review` (the adversarial linter). Domain
knowledge lives in `references/` (bundled), **never hardcoded in skill prose** —
same rule as the global image/video SSOT pattern.

## Reuse map — which installed skill each phase calls

| Phase skill | Reuses (installed skills) | Bundled references it reads |
|-------------|---------------------------|-----------------------------|
| `pitch-discovery` | mom-test, lean-startup | `vc-fundamentals.md`, `business-model.md` |
| `pitch-narrative` | storybrand, made-to-stick, influence | `deck-narrative.md`, `hormozi-offer.md` |
| `pitch-draft` | obviously-awesome, 100m-offers, monetizing-innovation, traction, blue-ocean-strategy | `business-model.md`, `unit-economics.md`, `investor-deck-rubric.md` |
| `pitch-review` | (none — this is the new logic) | `investor-deck-rubric.md`, `vc-review-rubric.md`, `unit-economics.md`, `vc-fundamentals.md` |
| `pitch-visual` | carousel-gen (`ai-image-carousel-prompt-gen`), gaspol-design *(optional)* | `deck-design.md` |
| `pitch-finish` | gaspol-knowledge *(optional)*, obsidian | — |

If a **required** installed skill above is missing at runtime, STOP and tell the
user — do NOT stub it or fabricate a replacement.

**Optional dependencies (graceful degrade, never hard-stop):**
- `gaspol-design` (palette/font/chart for slides) — if absent, `pitch-visual` falls back to the theming canon in `deck-design.md`, which is self-sufficient.
- `gaspol-knowledge` (the gaspol-dev cross-project KB skill) — if absent, `pitch-finish` skips the KB write-back and just notes it; the vault `hot.md` line via `obsidian` still happens.

## references/ index (the brain — GENERIC only)

- `investor-deck-rubric.md` — binary linter (PART A–D, 9 pass/fail slide checks).
- `vc-review-rubric.md` — scored judgment: 11-dim /55 + ABC dual-test + 4 conviction layers + cross-slide consistency + earned-secret gate + 30s-retell/dinner test.
- `hormozi-offer.md` — Grand Slam Offer + Value Equation.
- `deck-narrative.md` — storytelling canon + validated 14-slide spine.
- `business-model.md` — Business Model Canvas + revenue-model taxonomy + pricing.
- `unit-economics.md` — LTV/CAC/payback/burn-multiple/Rule-of-40/NRR + thresholds.
- `vc-fundamentals.md` — how VCs think, stage map, SAFE/dilution, term sheet, DD, objections.
- `deck-design.md` — slide visual canon + theming + nano-banana-pro slide-prompt archetype.
- `examples/good-deck.md` / `examples/bad-deck.md` — linter self-test fixtures (PASS / FAIL).

## Hard rules (gotchas)

1. **Never skip `pitch-review` before `pitch-finish`.** The gate is the whole point. A deck that has not passed the review verdict cannot be finished.
2. **Bundle only generic knowledge.** Single-event (Hub71), single-company (INDUSIA), and single-identity (any personal brand: specific hex colors, @handles, named portrait styles) knowledge is a **runtime input**, never committed to `references/`. CI guard: `grep -ri 'hub71\|indusia\|0F59B6\|spotlight portrait\|alisadikin' references/` must return nothing.
3. **Brand chrome is a runtime input.** Logo, palette, font, @handle — `pitch-visual` leaves a placeholder; it never bakes a specific brand.
4. **Render policy = full-slide image + QA safety net.** Each slide is one full nano-banana-pro image (text + numbers in-image). Bake exact headline + numbers quoted with font/size; if rendered, run the functional render-pass (inspect PNG → regen on defect).
5. **Image MCP is optional.** Always emit the prompts; PNG generation is a separable step. No MCP → manual-run note, not a failure.
6. **SKILL.md frontmatter = `name` + `description` only.** No other YAML keys (mirror gaspol-dev).

## Skill format

`skills/<name>/SKILL.md`: a `---` fenced YAML frontmatter (`name`, `description`)
then a markdown body. Description should carry the trigger phrases that route to
the skill.

## gaspol Ticket Counter

Prefix: CAT
Last ticket: CAT-1
