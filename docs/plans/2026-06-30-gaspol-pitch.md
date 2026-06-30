# gaspol-pitch — Design

> A pitch-deck orchestration plugin: same gaspol-dev pattern (brainstorm → plan → execute → review → gate → finish), but the domain is **investor/accelerator pitch decks** instead of software. Sequences already-installed marketing skills as the engine; the killer differentiator is an **adversarial investor-review linter pass** driven by `investor-deck-rubric.md`.

## Design

### Problem & intent
Building a deck VCs/accelerators actually fund is a repeatable *process*, not a one-off. Today that process lives in scattered skills (100m-offers, storybrand, traction, mom-test, obviously-awesome) + a rubric file, with no orchestration. `gaspol-pitch` wires them into a deterministic pipeline that BUILDS a deck and then ADVERSARIALLY scores it like a skeptical investor before you ever send it.

### Core principle (ponytail)
**Reuse, don't recreate.** Each pitch skill is THIN — it calls the relevant already-installed marketing skill for domain knowledge and only adds the orchestration glue + the rubric. The ONE genuinely new piece of logic is the `pitch-review` linter. Knowledge lives in `references/` (bundled), never hardcoded in skill prose (mirrors the CLAUDE.md image/video plugin rule).

### Deliverable (what the user gets)
1. **`deck.md`** — slide-by-slide copy + speaker notes (source of truth, version-controllable).
2. **`prompts/slide-NN.md`** — one **nano-banana-pro prompt per slide** (editorial/infographic design), authored from `deck-design.md` + carousel-gen + gaspol-design. The user pastes each into nano banana pro to render the slide.
3. **`review.md`** — the adversarial investor-linter report.
4. *(optional)* rendered `slide-NN.png` — only if an image MCP is available; otherwise the prompts are run manually. **No image MCP is required** — the plugin always produces the prompts; generation is a separable, optional step (carousel-gen "author prompt, consumer fulfills" pattern).

**Render policy = full-slide image + QA safety net (user-chosen):** each slide is ONE full nano-banana-pro image including its text/numbers (nano banana pro is SOTA at in-image text). Safety net against numeric drift: (a) the prompt bakes exact headline + numbers in quotes with font/size directives; (b) a **functional render-pass** (adopted from pitchkit) — if rendered, a fresh subagent inspects each PNG "assume there are issues, find them" (overflow, clipping, garbled text, wrong number) → regen on failure. `ponytail:` hybrid real-text overlay (Marp/pptxgenjs) is the upgrade path for number-heavy slides if drift persists; editable `.pptx` via pptxgenjs is a deferred optional export.

### Adopted from prior art (research 2026-06-30 — build, don't reinvent)
Field sweep confirmed **no tool does the full orchestration + per-slide image prompts + Hormozi/BMC/unit-econ** — so we build, but vendor proven MIT-licensed knowledge (ideas/frameworks aren't copyrightable; verbatim text only from MIT sources, attributed):
- **cc-skills-vc-fundraising** (MIT) → **11-dimension scored rubric (/55)** + "30-second retell" narrative gate + 10 first-principles → into `vc-review-rubric.md`.
- **founder-pitch-deck-review** → **cross-slide consistency checks** (TAM ↔ traction ↔ ask must reconcile) + AI-COGS/unit-econ validation → into `vc-review-rubric.md`.
- **nock** (MIT) → **earned-secret / insight-count gate** (≥1 earned secret across tech/market/GTM) + "dinner test" → into `vc-review-rubric.md`.
- **kaustav reviewer / Stevekaplan** → **ABC dual-test** (per slide: reduces investor risk? increases outcome value?) + **4 Conviction Layers** (Market/Insight/Founder/Execution) + Deck-Economy merge/cut/split → into `vc-review-rubric.md`.
- **PitchPilot** → **investor Q&A-simulation pass** (toughest questions per slide, flag weak spots) → pitch-review adversarial stage.
- **FluidDocs deck-builder** (MIT) → **type-correct deck spine** (validated 14-slide pitch backbone) → `deck-narrative.md`; **multi-reviewer finish** (Brand/Copy/Layout subagents) → pitch-finish.
- **zolidar-pitch-builder** (MIT) → **discovery contract** (`narrative.md` MUST-HAVE + probing question bank: "how painful—quantify / why 10x / why hard to replicate") + Khosla+Sequoia grounding → pitch-discovery + pitch-narrative.
- **pitchkit** (MIT) → **functional render-pass** (render slide → inspect image for defects) → pitch-visual.
- **Anthropic pptx skill Design Ideas** + **Mck-ppt-design-skill** → theming canon (60/30/10 color, dark/light sandwich, type scale, 0.5" grid, one motif, no accent-line-under-title) + layout patterns → `deck-design.md`.
- **Reference corpora** (further reading, NOT bundled — too large): joelparkerhenderson/pitch-deck (advice), midovislam/awesome-pitch-decks (754 real decks). Linked in README.

### Architecture: multi-skill orchestrator (mirrors gaspol-dev)

```
gaspol-pitch/
  .claude-plugin/plugin.json     # manifest
  CLAUDE.md                      # plugin rules + reuse map + gotchas
  README.md
  LICENSE
  references/                    # the BRAIN (bundled knowledge — GENERIC only, no single-event specifics)
    investor-deck-rubric.md      # binary linter (PART A–D): 9 pass/fail slide checks
    vc-review-rubric.md          # SCORED judgment: 11-dim /55 + ABC dual-test + 4 conviction layers + cross-slide consistency + earned-secret/insight-count + 30s-retell/dinner test
    hormozi-offer.md             # Grand Slam Offer + Value Equation
    deck-narrative.md            # storytelling canon: Sequoia/YC/Kawasaki/Raskin/Duarte + Khosla + validated 14-slide spine
    business-model.md            # Business Model Canvas + revenue-model taxonomy + pricing
    unit-economics.md            # LTV/CAC/payback/burn-multiple/Rule-of-40/NRR + thresholds
    vc-fundamentals.md           # how VCs think, stage map, SAFE/dilution, term sheet, DD, objections
    deck-design.md               # slide visual canon + theming (60/30/10, type scale, grid, motif) + nano-banana-pro SLIDE prompt archetype + slide-variant taxonomy
    examples/
      good-deck.md               # fictional PASS fixture (linter self-test)
      bad-deck.md                # fictional FAIL fixture (linter self-test)
  skills/
    gaspol-pitch/                # orchestrator — routes to the 6 phase skills
    pitch-discovery/             # gather facts, pressure-test problem
    pitch-narrative/             # choose slide sequence + thesis spine
    pitch-draft/                 # write Marp markdown + speaker notes
    pitch-review/                # ADVERSARIAL investor linter (the differentiator)
    pitch-visual/                # optional: hand off cover/keyframes to carousel-gen
    pitch-finish/                # export PDF/PPTX + KB learn + vault writeback
  docs/plans/                    # this file + future plans
```

### The pipeline (6 phases + orchestrator)

| # | Skill | Job (ONE) | Reuses (installed) | Output |
|---|-------|-----------|--------------------|--------|
| 0 | **gaspol-pitch** | Orchestrate: detect intent, run phases in order, enforce review gate | — | routes |
| 1 | **pitch-discovery** | Collect inputs via a **structured discovery contract** (narrative.md MUST-HAVE + probing question bank: "who hurts / quantify the pain / why 10x / why hard to replicate" — adopted from zolidar) + target profile (inline schema; user supplies their target VC/accelerator at runtime — NOT bundled); flag gaps scoped to what THIS stage's investors must see (vc-fundamentals stage map) | mom-test, lean-startup | `discovery.md` (facts + GAP list) |
| 2 | **pitch-narrative** | Pick slide sequence from the **validated 14-slide spine** (traction-first if metrics exceptional) + write the thesis spine (headlines that alone reconstruct the thesis); ground in Khosla (emotional) + Sequoia (systematic) | storybrand, made-to-stick, hormozi-offer, deck-narrative.md | `narrative-plan.md` |
| 3 | **pitch-draft** | Write the slides — Marp markdown (one slide per `---`) + speaker notes, one job/slide, contextualized numbers | obviously-awesome, 100m-offers, monetizing-innovation, traction, blue-ocean, business-model.md, unit-economics.md | `deck.md` |
| 4 | **pitch-review** | **Adversarial investor judge.** Binary linter (rubric PART C, 9 checks) + **scored 11-dim rubric /55** + **ABC dual-test** per slide + **4 conviction layers** + PART B signal thresholds (LTV≥3×CAC, payback<12mo, bottom-up TAM) + **cross-slide consistency** (TAM↔traction↔ask) + **earned-secret/insight-count gate** + "30-second retell"/"dinner test" + a **skeptic subagent Q&A-simulation** (toughest investor questions per slide, Forwardable Test). | investor-deck-rubric + vc-review-rubric + unit-economics + vc-fundamentals | `review.md` (binary table + score/55 + Q&A weak-spots + BLOCKING verdict) |
| 5 | **pitch-gate** *(folded into review)* | If any binary fail, score below threshold, unreconciled inconsistency, or missing earned secret → loop back to pitch-draft with specific fixes. Else PASS → proceed. | — | gate verdict |
| 6 | **pitch-visual** | Author a **nano-banana-pro prompt per slide** (editorial/infographic archetype from `deck-design.md`, NOT cinematic portrait; exact headline+numbers quoted with font/size). Reuses carousel-gen as SSOT + gaspol-design for palette/font/chart. MCP-OPTIONAL: if an image MCP is present, auto-generate PNGs + run the **functional render-pass** (subagent inspects each PNG for defects → regen); else emit prompts for manual run. | carousel-gen, gaspol-design, deck-design.md | `prompts/slide-NN.md` (+ optional `slide-NN.png`) |
| 7 | **pitch-finish** | Gate on pitch-review PASS. Optional **multi-reviewer finish** (Brand/Copy/Layout subagents in parallel — adopted from FluidDocs). If PNGs exist → bundle; else deliver `deck.md` + `prompts/`. Final ≤14-slide checklist; write reusable pattern to gaspol-knowledge KB + one line to vault `hot.md` | gaspol-knowledge, obsidian | deck.md + prompts/ (+ optional PNGs) + KB entry |

### The differentiator — pitch-review (adversarial investor judge)
The load-bearing phase. It does NOT trust the draft's self-report. Layers (binary → scored → cross-slide → adversarial):
1. **Binary lint** — each slide vs `investor-deck-rubric.md` PART C (9 pass/fail: Value-Anchor, Pyramid, Assertion-vs-Topic, One-Message, Linguistic-Fluff, 5-Second, Data-Parsing, Platform-Risk, Forwardable).
2. **Scored judgment** — `vc-review-rubric.md`: 11-dimension rubric scored /55 + **ABC dual-test** per slide (reduces investor risk? increases outcome value?) + **4 conviction layers** (Market/Insight/Founder/Execution coverage).
3. **Signal thresholds** — PART B + `unit-economics.md`: LTV≥3×CAC, payback<12mo, bottom-up TAM, NRR/burn-multiple/Rule-of-40 where relevant.
4. **Cross-slide consistency** — TAM ↔ traction ↔ ask must reconcile; flag contradictions no per-slide check catches.
5. **Earned-secret / insight-count gate** — must carry ≥1 non-obvious earned secret across tech/market/GTM (nock); "is it earned or faked?" follow-ups.
6. **Narrative gates** — "30-second retell" + "dinner test": can a VC retell the thesis cold? Headlines-only reconstruct the thesis?
7. **Skeptic subagent Q&A-simulation** (PitchPilot) — generate the toughest investor questions per slide; surface the weakest 3 claims; Forwardable Test (zero verbal context).
8. **BLOCKING verdict** — any binary fail, sub-threshold score, unreconciled inconsistency, or missing earned secret → loop back to draft with the exact fix list. Mirrors gaspol-dev's adversarial refutation pass.

### Knowledge bundling (references/) — GENERIC ONLY
**Rule:** bundle only reusable, target-agnostic knowledge. Single-event/single-company specifics (Hub71, INDUSIA) stay in their project folder and are passed in as **runtime input**, never bundled.
- `investor-deck-rubric.md` — copied verbatim from current project file; the **binary linter** (PART A–D, 9 pass/fail slide checks).
- `vc-review-rubric.md` — **scored judgment brain** (adopted, reworded from MIT sources): 11-dimension rubric scored /55 + ABC dual-test (per slide: reduces investor risk? increases outcome value?) + 4 Conviction Layers (Market/Insight/Founder/Execution) + cross-slide consistency checks (TAM↔traction↔ask reconcile) + earned-secret/insight-count gate (≥1 across tech/market/GTM) + "30-second retell" + "dinner test" narrative gates. Complements the binary linter; feeds pitch-review.
- `hormozi-offer.md` — from `alex-hormozi-pitch-methodology.md` §1–6, Hub71-mapping note dropped so the offer methodology stays generic.
- `deck-narrative.md` — pitch-deck storytelling canon: Sequoia template, YC outline, Kawasaki 10/20/30, Andy Raskin 5-step strategic narrative, Khosla emotional-storytelling, Duarte sparkline, why-now + demo craft, AND a **validated 14-slide type-correct pitch spine** (FluidDocs, audited vs decks that closed rounds). Feeds pitch-narrative.
- `business-model.md` — Business Model Canvas (9 blocks), revenue-model taxonomy (subscription/usage/transaction/marketplace take-rate/licensing/freemium), pricing (value-based, van Westendorp). Feeds pitch-discovery + pitch-draft.
- `unit-economics.md` — LTV, CAC, payback, contribution margin, cohort retention, burn multiple, Rule of 40, magic number, NRR + healthy thresholds per model type (SaaS / marketplace / hardware / transactional). Volatile benchmarks marked "rule of thumb, verify". Feeds pitch-draft + pitch-review (signal scoring).
- `vc-fundamentals.md` — how VCs think (power law, fund + ownership math), stage map (pre-seed→seed→A→B) with what each stage must see, SAFE / valuation / dilution, term-sheet basics, due-diligence checklist, common objection handling. Feeds pitch-discovery (GAP list) + pitch-review (DD/objection pass).
- `deck-design.md` — slide visual canon + the **nano-banana-pro SLIDE prompt archetype** (editorial/infographic, 16:9). Includes: **theming canon** (adopted from Anthropic pptx Design Ideas + Mck-ppt-design-skill) — 60/30/10 color dominance + curated palette pairs, dark/light sandwich, type scale (title 36–44 / section 20–24 / body 14–16 / caption 10–12), 0.5" grid + consistent gaps, ONE repeated motif, **never an accent line under the title** (AI-slop tell), never a text-only slide; **slide-variant taxonomy** (title/section/cards-3/split/stats/kpi-hero/comparison/matrix/chart/flow/team/ask); the 5-section slide-prompt template (LAYOUT / VISUAL-ELEMENTS / STYLE+PALETTE / TEXT(exact headline+numbers verbatim, quoted, font/size) / CONSTRAINTS 16:9 safe-margins); per-slide-type visual recipe; data-viz chart-choice; WOW-score + anti-AI-look. Generic only — **brand chrome (logo/handle/colors/font) is a runtime brand input**, never baked. Distilled from vault `image-gen-shared.md` (generic principles only, NO Ali-personal brand). Feeds pitch-visual.
- `examples/good-deck.md` — a fictional, target-agnostic deck ("Acme") that PASSES the linter — the self-test positive case.
- `examples/bad-deck.md` — a fictional deck with planted rubric violations that FAILS — the self-test negative case.

**Explicitly NOT bundled (kept project-local, used at runtime):**
- Hub71 selection pattern (`STRATEGY-maximize-selection.md`) → it's one accelerator's profile. The user supplies a target profile per run; pitch-discovery defines the field schema inline (selection criteria, required deck blocks, disqualifiers, alignment language) and reads a user-provided target file if one exists.
- INDUSIA deck (`POSITIONING/DECK/SLIDES`) → one company's deck = input to the skill, not plugin knowledge.

### Data integration map
| Component | Data source | Existing? | Notes |
|-----------|-------------|-----------|-------|
| rubric brain | `investor-deck-rubric.md` (this project) | ✅ | copy into references/ |
| offer methodology | `alex-hormozi-pitch-methodology.md` §1–6 | ✅ | copy → hormozi-offer.md, drop Hub71 note |
| good fixture | hand-written, fictional "Acme" | ➕ new | references/examples/good-deck.md |
| bad fixture | hand-written, fictional + planted violations | ➕ new | references/examples/bad-deck.md |
| target profile | user-supplied at runtime (NOT bundled) | ⚙️ runtime | pitch-discovery defines field schema inline |
| narrative engine | storybrand, made-to-stick, hormozi | ✅ installed | called, not recreated |
| offer/positioning | 100m-offers, obviously-awesome, monetizing-innovation | ✅ installed | called |
| discovery engine | mom-test, lean-startup | ✅ installed | called |
| visuals | carousel-gen (`ai-image-carousel-prompt-gen`) | ✅ installed | optional handoff |
| export | Marp CLI (`@marp-team/marp-cli`) | ⚠️ may need install | markdown→PDF/PPTX; lazy fallback = leave deck.md if Marp absent |

### Scope cuts (ponytail — explicitly NOT building)
- No single-event knowledge in the plugin — Hub71/INDUSIA stay project-local, passed as runtime input.
- No new design/rendering engine — Marp does markdown→PDF/PPTX; carousel-gen does stills.
- No new marketing knowledge — all reused from installed skills.
- pitch-gate is not its own skill — it's the verdict branch inside pitch-review.
- pitch-visual is optional — skipped unless user wants rendered visuals.
- No `targets/` folder — target profile is a runtime input + inline field schema, not bundled files.

### Open / deferred
- Marp install: defer until pitch-finish actually runs; if `marp` missing, finish leaves `deck.md` + prints install hint (`npm i -g @marp-team/marp-cli`). `ponytail:` no export dependency until needed.
- Per-target profiles (Hub71/YC/Techstars/a16z) = optional user-authored files passed at runtime; the plugin ships none.

## Implementation Plan

> **For Claude:** REQUIRED SKILL: Use gaspol-execute to implement this plan.
> **CRITICAL:** This plan bundles real knowledge files + reuses installed skills.
> NEVER substitute a placeholder rubric or fabricate skill names. If an installed
> skill referenced below is missing at execute time, STOP and ask — do not stub it.

### Goal
Ship `gaspol-pitch` — a Claude Code plugin that orchestrates investor/accelerator pitch-deck creation through 6 thin phase-skills + 1 orchestrator, reusing already-installed marketing skills as the engine, with an adversarial investor-review linter as the differentiator. Output = Marp markdown deck that passes a rubric-driven skeptic pass before it's sent.

### Architecture Context (from gaspol-dev + global CLAUDE.md)
- **Plugin format** (mirror `gaspol-dev/1.8.0`): `.claude-plugin/plugin.json` (name/description/version/author/license/keywords) + `skills/<name>/SKILL.md` (YAML frontmatter: `name`, `description` only) + markdown body. No hooks/agents needed for v0.1.
- **Reuse rule (ponytail + CLAUDE.md image/video SSOT pattern):** skills stay THIN, call installed skills + bundled `references/`, never hardcode domain knowledge in prose.
- **Knowledge sources (GENERIC, verified to exist):** `investor-deck-rubric.md`, `alex-hormozi-pitch-methodology.md` §1–6. (Hub71 `STRATEGY-*` + INDUSIA `POSITIONING/DECK/SLIDES` = single-event, project-local, NOT bundled — runtime input only.)
- **Installed engine skills (reused, not recreated):** mom-test, lean-startup, storybrand, made-to-stick, influence, obviously-awesome, 100m-offers, 100m-leads, monetizing-innovation, traction, blue-ocean-strategy, crossing-the-chasm, carousel-gen (`ai-image-carousel-prompt-gen`), gaspol-design (palette/font/chart for slides).
- **Image render (optional, runtime):** nano banana pro via `indusia-image-gen` or `higgsfield` MCP — confirm model id at runtime; absent → user runs prompts manually.

### Tech Stack
- Markdown + YAML frontmatter (skill files). JSON (manifest). Marp markdown convention for `deck.md` (`---` slide breaks, `<!-- notes -->`) — as the editable source, NOT the render target. No build system. **Render = full-slide nano-banana-pro images** (per-slide prompt; MCP-optional). `ponytail:` editable `.pptx` via pptxgenjs deferred to a later version.

### Data Integration Map
| Feature | Data Source | Reference/Skill | Exists? | Action |
|---------|-------------|-----------------|---------|--------|
| rubric brain | `investor-deck-rubric.md` (project root) | references/investor-deck-rubric.md | Yes | Copy verbatim |
| offer methodology | `alex-hormozi-pitch-methodology.md` §1–6 | references/hormozi-offer.md | Yes | Copy, drop Hub71 note (stays generic) |
| good fixture | hand-written, fictional "Acme" | references/examples/good-deck.md | New | Author (PASS case) |
| bad fixture | hand-written, fictional + planted violations | references/examples/bad-deck.md | New | Author (FAIL case) |
| target profile | user-supplied at runtime | pitch-discovery inline schema | Runtime | NOT bundled — field schema inline |
| discovery engine | mom-test, lean-startup | installed skills | Yes | Call from pitch-discovery |
| narrative engine | storybrand, made-to-stick, influence | installed skills | Yes | Call from pitch-narrative |
| offer/positioning | obviously-awesome, 100m-offers, monetizing-innovation, traction, blue-ocean | installed skills | Yes | Call from pitch-draft |
| binary linter | rubric PART B+C+D | references/investor-deck-rubric.md | Yes | pitch-review reads it |
| scored judgment | 11-dim/55 + ABC + conviction + consistency + earned-secret | references/vc-review-rubric.md | New | author (adopt MIT prior art, attributed) |
| discovery contract | probing-Q bank + narrative.md (zolidar, MIT) | pitch-discovery | Adopt | reword into skill |
| validated spine | 14-slide type-correct (FluidDocs, MIT) | references/deck-narrative.md | Adopt | into deck-narrative |
| Q&A simulation | toughest-questions pass (PitchPilot) | pitch-review subagent | Adopt | idea only (no license) |
| slide design canon | vault image-gen-shared + Anthropic pptx + Mck layout | references/deck-design.md | New | author (no Ali-brand) |
| per-slide nano prompt | deck-design.md + carousel-gen + gaspol-design | pitch-visual | Yes (skills) | author prompt/slide |
| image render (optional) | nano banana pro | `indusia-image-gen` / `higgsfield` MCP | Maybe | MCP-optional; manual fallback |
| functional render-pass | inspect PNG→regen (pitchkit, MIT) | pitch-visual subagent | Adopt | defect-catch loop |
| multi-reviewer finish | Brand/Copy/Layout (FluidDocs, MIT) | pitch-finish subagents | Adopt | optional parallel review |
| brand chrome | user-supplied at runtime | pitch-visual placeholder | Runtime | NOT baked |

### Verification model (plugin authoring — no tsc/jest)
A "test" here = a **runnable assertion** about the artifacts:
- `python -c "import json,sys; json.load(open('.claude-plugin/plugin.json'))"` → manifest parses.
- A tiny YAML-frontmatter check per SKILL.md (`---` fenced block, has `name:` + `description:`).
- `references/` paths cited inside skills actually resolve on disk (grep cited paths → `test -f`).
- **Linter self-test (the real test):** `references/examples/bad-deck.md` (a deliberately-broken fixture) MUST trip specific PART-C checks; `references/examples/good-deck.md` (fictional "Acme") MUST pass them. pitch-review is correct only if it FAILS the bad one and PASSES the good one.

---

### Phase A: Scaffold + manifest
**Estimated time:** 6 min
**Files:**
- Create: `.claude-plugin/plugin.json`, `LICENSE` (MIT), `.gitignore`, `README.md`, `CLAUDE.md`
**Steps:**
1. Write failing test: `python -c "import json; json.load(open('.claude-plugin/plugin.json'))"`. Expected error: `FileNotFoundError: .claude-plugin/plugin.json`.
2. Run it, confirm FileNotFoundError.
3. Create `plugin.json` (name `gaspol-pitch`, version `0.1.0`, author Ali Sadikin, license MIT, keywords: pitch-deck, investor, fundraising, accelerator, hormozi, adversarial-review, claude-code) + LICENSE + `.gitignore` (`.DS_Store`, `*.pdf`, `*.pptx`, `.gaspol/`) + README (what/why/quickstart) + CLAUDE.md (reuse map + gotchas + references index).
4. Run test, confirm manifest parses.
5. Commit: "feat: scaffold gaspol-pitch plugin (manifest, license, readme, claude.md)".
**Verification:**
- [ ] `plugin.json` parses as JSON, has name/description/version/author/license/keywords
- [ ] CLAUDE.md lists the reuse map (which installed skill each phase calls) + references index
- [ ] No placeholder/TODO text in README/CLAUDE.md
- [ ] `.DS_Store` ignored

### Phase B: Bundle references/ (the brain)
**Estimated time:** 46 min
**Attribution:** ideas/frameworks adopted from prior art (see "Adopted from prior art"). Verbatim text only from MIT sources (cc-skills-vc-fundraising, FluidDocs, nock, zolidar, pitchkit) with a one-line credit in each file's header; no-license sources (kaustav, Stevekaplan, PitchPilot, founder-review) → reworded in our own words.
**Files:**
- Create: `references/investor-deck-rubric.md`, `references/vc-review-rubric.md`, `references/hormozi-offer.md`, `references/deck-narrative.md`, `references/business-model.md`, `references/unit-economics.md`, `references/vc-fundamentals.md`, `references/deck-design.md`, `references/examples/good-deck.md`, `references/examples/bad-deck.md`
**Steps:**
1. Write failing test: `for f in references/investor-deck-rubric.md references/vc-review-rubric.md references/hormozi-offer.md references/deck-narrative.md references/business-model.md references/unit-economics.md references/vc-fundamentals.md references/deck-design.md references/examples/good-deck.md references/examples/bad-deck.md; do test -f "$f" || { echo "MISSING $f"; exit 1; }; done`. Expected error: `MISSING references/investor-deck-rubric.md`.
2. Run, confirm MISSING.
3. **Copy-derived:** `investor-deck-rubric.md` verbatim; `hormozi-offer.md` from `alex-hormozi-pitch-methodology.md` §1–6 (drop Hub71 note). **Author canon (high-signal, checklist/threshold style, NOT essays):**
   - `vc-review-rubric.md` — the **scored judgment brain** for pitch-review: 11-dimension rubric each scored 1–5 (=/55) with letter grade + per-dim fix (cc-skills-vc-fundraising); **ABC dual-test** per slide (reduces investor risk? increases value of outcome?); **4 Conviction Layers** scorecard (Market/Insight/Founder/Execution — flag Missing); **cross-slide consistency** checklist (TAM↔traction↔ask reconcile; AI-COGS sanity); **earned-secret/insight-count gate** (≥1 across tech/market/GTM, earned-vs-faked test — nock); **narrative gates** ("30-second retell", "dinner test"); **Deck-Economy** merge/cut/split + slide-count. Each item = how to score + what fails.
   - `deck-narrative.md` — Sequoia 10-slide template, YC standard outline, Kawasaki 10/20/30 rule, Andy Raskin 5-step strategic narrative (name the enemy / promised land / features-as-magic-gifts / evidence / the stakes), Khosla emotional-storytelling principles, Duarte sparkline (what-is vs what-could-be), why-now + live-demo craft, AND the **validated 14-slide type-correct pitch spine** (Title·Problem·Solution·Market·Product·Traction·Business-Model·Competition·GTM·Team·Financials·Ask·Vision·Appendix — FluidDocs/zolidar, audited vs closed rounds).
   - `business-model.md` — Business Model Canvas 9 blocks (each: 1-line def + the question it answers), revenue-model taxonomy table (subscription/usage/transaction/marketplace take-rate/licensing/freemium — with margin profile + when-to-use), pricing methods (cost-plus vs value-based vs van Westendorp PSM).
   - `unit-economics.md` — formulas + healthy thresholds: LTV, CAC, LTV:CAC (≥3×), CAC payback (<12mo), gross margin by model, contribution margin, cohort retention, NRR (>100% good, >120% great), burn multiple (<1 great / 1–2 ok), Rule of 40, magic number (>0.75). Each volatile benchmark tagged "rule of thumb, verify per stage/sector".
   - `vc-fundamentals.md` — power-law fund math (why VCs need fund-returners → ownership × outcome), stage map table (pre-seed→seed→A→B: typical raise, valuation band "rule of thumb", what each stage must SEE), SAFE mechanics (cap/discount/MFN), dilution math, term-sheet key terms (liq pref, pro-rata, board, option pool), DD checklist, top investor objections + how a deck pre-empts each.
   - `deck-design.md` — slide visual canon + the nano-banana-pro **SLIDE prompt archetype** (16:9 editorial/infographic, NOT cinematic portrait). Include: **theming canon** (Anthropic pptx Design Ideas + Mck-ppt-design-skill) — 60/30/10 color dominance + 2–3 curated palette pairs, dark/light sandwich, type scale (title 36–44 / section 20–24 / body 14–16 / caption 10–12), 0.5" min margins + consistent gaps, ONE repeated motif, **never an accent line under the title**, never a text-only slide; **slide-variant taxonomy** (title/section/cards-3/split/stats/kpi-hero/comparison/matrix/chart/flow/team/ask); the 5-section slide-prompt template (LAYOUT / VISUAL-ELEMENTS / STYLE+PALETTE / TEXT(exact headline+numbers quoted, font/size) / CONSTRAINTS(16:9, safe margins)); per-slide-type visual recipe; data-viz chart-choice; WOW-score gate + anti-AI-look; **functional render-pass spec** (after gen, inspect image for overflow/clipping/garbled-text/wrong-number → regen); explicit rule that **brand chrome (logo/@handle/palette/font) is a runtime brand input placeholder, never baked**. Distill GENERIC principles from vault `image-gen-shared.md` only — NO Ali-personal brand (#0F59B6, Spotlight Portrait, @handle, alisadikin).
   - **Fixtures:** `examples/good-deck.md` — fictional "Acme" Marp deck satisfying the rubric (assertion headlines, contextualized numbers, bottom-up TAM, exact ask) = PASS. `examples/bad-deck.md` — 4-slide fixture violating: Assertion-vs-Topic ("Market Size" label), Linguistic-Fluff ("synergy/world-class"), Data-Parsing ("four million"), top-down TAM, "raising $2M to grow". NO Hub71/INDUSIA in either.
4. Run test, confirm all 10 resolve.
5. Commit: "feat: bundle generic pitch+businessmodel+VC+design knowledge into references/ (adopted from prior art, attributed)".
**Verification:**
- [ ] All 10 reference files exist and are non-empty (`wc -c` > 0)
- [ ] `vc-review-rubric.md` has the 11-dim scored rubric (/55) + ABC dual-test + 4 conviction layers + cross-slide consistency + earned-secret gate + 30s-retell/dinner test
- [ ] MIT-sourced files carry a one-line attribution header; no verbatim text from no-license sources
- [ ] `hormozi-offer.md` contains the Value Equation block, NO Hub71-specific text
- [ ] `unit-economics.md` states the hard thresholds (LTV:CAC≥3×, payback<12mo, NRR, burn multiple, Rule of 40) and tags volatile numbers "verify"
- [ ] `vc-fundamentals.md` has the stage map table + SAFE mechanics + DD checklist + objection list
- [ ] `deck-design.md` has theming canon (60/30/10, type scale, grid, motif, no-accent-line) + slide-variant taxonomy + 5-section SLIDE prompt template + functional-render-pass spec + brand-chrome-as-runtime; NO Ali-personal brand baked
- [ ] `deck-narrative.md` includes the validated 14-slide spine; checklist/table style, not essays
- [ ] NO single-event/single-identity content bundled — `grep -ri 'hub71\|indusia\|0F59B6\|spotlight portrait\|alisadikin' references/` returns nothing
- [ ] `examples/good-deck.md` is valid Marp (uses `---` slide breaks) and passes the rubric
- [ ] `examples/bad-deck.md` contains ≥4 deliberate rubric violations (documented in a trailing comment)

### Phase C: Orchestrator skill (gaspol-pitch)
**Estimated time:** 8 min
**Files:**
- Create: `skills/gaspol-pitch/SKILL.md`
**Steps:**
1. Write failing test: `awk 'NR==1&&/^---$/{f=1} /^name:/{n=1} /^description:/{d=1} END{exit (f&&n&&d)?0:1}' skills/gaspol-pitch/SKILL.md`. Expected error: file missing → awk exits 1.
2. Run, confirm fail.
3. Write SKILL.md: frontmatter (`name: gaspol-pitch`, description with trigger phrases "pitch deck", "investor deck", "fundraising deck", "buatkan deck"). Body: announce line; the 6-phase pipeline table; routing logic (detect what user has → enter at right phase); HARD-GATE = never skip pitch-review before finish; reference index pointing to `references/`. State explicitly it CALLS the phase skills in order and enforces the review gate-loop.
4. Run test, confirm frontmatter valid.
5. Commit: "feat: add gaspol-pitch orchestrator skill".
**Verification:**
- [ ] Frontmatter is a fenced `---` block with `name` + `description`
- [ ] Body documents all 6 phases in order + the review gate-loop
- [ ] Lists which installed skill each phase reuses
- [ ] No invented skill names (cross-check against installed list)

### Phase D: pitch-discovery + pitch-narrative
**Estimated time:** 12 min
**Files:**
- Create: `skills/pitch-discovery/SKILL.md`, `skills/pitch-narrative/SKILL.md`
**Steps:**
1. Write failing test: frontmatter-valid awk check (as Phase C) over both files. Expected: both missing → exit 1.
2. Run, confirm fail.
3. `pitch-discovery`: run a **structured discovery contract** (adopted from zolidar) — a `narrative.md` MUST-HAVE intake + a **probing question bank** ("who experiences this / how painful—quantify it / what do they do today / why isn't current solution working / why is yours 10x / why hard to replicate"); collect company/problem/solution/traction/team/ask + **target profile supplied at runtime** (field schema INLINE: selection criteria, required deck blocks, disqualifiers, alignment language; read a user-provided target file if passed; bundle NO specific target). Call mom-test + lean-startup to pressure-test the problem; read `references/vc-fundamentals.md` (stage map) to scope the **GAP list** to what THIS stage's investors must see; output `discovery.md`. `pitch-narrative`: pick sequence from the **validated 14-slide spine** in `references/deck-narrative.md` (traction-first if metrics exceptional); ground in Khosla (emotional) + Sequoia (systematic); call storybrand + made-to-stick + hormozi-offer Value Equation; output `narrative-plan.md` = the headline thesis spine (each a complete assertion sentence). Both cite `references/` by relative path.
4. Run test, confirm both valid.
5. Commit: "feat: add pitch-discovery (probing-question contract) + pitch-narrative (validated spine)".
**Verification:**
- [ ] Both SKILL.md frontmatters valid
- [ ] pitch-discovery ships the probing question bank + GAP list section + runtime target schema
- [ ] pitch-narrative uses the 14-slide spine + "headlines alone reconstruct the thesis" + traction-first rule + Khosla/Sequoia grounding
- [ ] Cited `references/` paths resolve (`test -f`)

### Phase E: pitch-draft
**Estimated time:** 8 min
**Files:**
- Create: `skills/pitch-draft/SKILL.md`
**Steps:**
1. Write failing test: frontmatter awk check on `skills/pitch-draft/SKILL.md`. Expected: missing → exit 1.
2. Run, confirm fail.
3. Write SKILL.md: input = `narrative-plan.md`; produce `deck.md` in Marp (one slide per `---`, `<!-- notes: -->` speaker notes), ONE job per slide (rubric PART A), contextualized numbers, ≤12–15 slides, and cover any extra deck blocks the runtime target profile requires (generic — read from discovery.md, not hardcoded). Calls obviously-awesome (positioning headline), 100m-offers (offer slide), monetizing-innovation (pricing/unit-econ), traction + blue-ocean (market/competition), and reads `references/business-model.md` + `references/unit-economics.md` for the business-model + unit-econ slides. Bullet discipline: ≤5 bullets/slide, <40 words, active voice.
4. Run test, confirm valid.
5. Commit: "feat: add pitch-draft skill (Marp markdown + speaker notes)".
**Verification:**
- [ ] Frontmatter valid
- [ ] Output spec = Marp markdown with `---` breaks + speaker-note convention
- [ ] Enforces one-job-per-slide + ≤15 slides + bullet discipline (cites rubric PART A/D)
- [ ] Names only installed skills for reuse

### Phase F: pitch-review — adversarial linter (DIFFERENTIATOR + self-test)
**Estimated time:** 15 min
**Files:**
- Create: `skills/pitch-review/SKILL.md`
- Test fixture: `references/examples/bad-deck.md` (from Phase B), `references/examples/good-deck.md`
**Steps:**
1. Write failing test (the real one): a check script that simulates the linter contract — grep `bad-deck.md` for the planted violations and assert pitch-review's documented rules would catch each. Concretely: `grep -Eqi 'world-class|synergy|four million|Market Size|raising \$2M to grow' references/examples/bad-deck.md` MUST match (fixture has the bait) AND `skills/pitch-review/SKILL.md` MUST enumerate the matching PART-C rule for each. Expected error at start: `skills/pitch-review/SKILL.md` missing → fail.
2. Run, confirm fail.
3. Write SKILL.md: load `references/investor-deck-rubric.md` (binary) + `references/vc-review-rubric.md` (scored) + `references/unit-economics.md` (thresholds) + `references/vc-fundamentals.md` (DD/objections). Procedure: (a) **binary** — per-slide 9 PART-C checks; (b) **scored** — 11-dim rubric /55 + ABC dual-test + 4 conviction layers; (c) **signals** — PART-B vs hard thresholds (LTV≥3×CAC, payback<12mo, bottom-up TAM, 2×QoQ, NRR/burn-multiple/Rule-of-40) per unit-economics.md; (d) **cross-slide consistency** — TAM↔traction↔ask reconcile; (e) **earned-secret/insight-count gate** + "30-second retell"/"dinner test"; (f) **spawn a skeptic subagent** for the Q&A-simulation (toughest investor questions per slide + weakest-3-claims + Forwardable Test, zero context); (g) emit `review.md` = binary table + score/55 + Q&A weak-spots + BLOCKING verdict; (h) gate-loop: any binary fail OR sub-threshold score OR unreconciled inconsistency OR missing earned secret → return to pitch-draft with the exact fix list, else PASS. Self-test note: `bad-deck.md` → fails Assertion-vs-Topic, Linguistic-Fluff, Data-Parsing, TAM, Ask; `good-deck.md` → PASS.
4. Run test, confirm the fixture trips the documented rules.
5. Commit: "feat: add pitch-review adversarial investor judge (binary+scored+Q&A-sim — the differentiator)".
**Verification:**
- [ ] Frontmatter valid
- [ ] Enumerates 9 PART-C binary checks + the 11-dim scored rubric + ABC dual-test + 4 conviction layers (no hand-wave)
- [ ] Runs cross-slide consistency + earned-secret gate + 30s-retell/dinner test
- [ ] Spawns the skeptic-subagent Q&A-simulation (per-slide tough questions + Forwardable Test)
- [ ] Emits a BLOCKING verdict (binary fail / sub-threshold / inconsistency / no earned secret) + gate-loop to pitch-draft
- [ ] Self-test documented: `bad-deck.md` FAILS the named checks, `good-deck.md` PASSES
- [ ] `grep` over `bad-deck.md` matches all planted violations (fixture is real, not empty)

### Phase G: pitch-visual + pitch-finish
**Estimated time:** 14 min
**Files:**
- Create: `skills/pitch-visual/SKILL.md`, `skills/pitch-finish/SKILL.md`
**Steps:**
1. Write failing test: frontmatter awk check over both. Expected: missing → exit 1.
2. Run, confirm fail.
3. `pitch-visual` (core rendering, runs after review PASS): for EACH slide in `deck.md`, author a **nano-banana-pro prompt** using `references/deck-design.md` (slide archetype + 5-section template + theming canon + variant taxonomy) — reuse carousel-gen as the SSOT prompt machinery + gaspol-design for palette/font/chart; bake the slide's EXACT headline + numbers into the prompt's TEXT section **quoted with font/size** (verbatim render); leave brand-chrome as a runtime placeholder. Write one `prompts/slide-NN.md` per slide. **MCP-optional fulfillment + functional render-pass:** detect an image MCP (`indusia-image-gen` / `higgsfield` — confirm a nano-banana-pro model id via `list_image_models`/`models_explore`); if present, auto-generate `slide-NN.png` AND run the **functional render-pass** (a fresh subagent inspects each PNG "assume there are issues — find them": overflow, clipping, garbled text, wrong/illegible number → regen that slide, max 2 retries, then flag for manual fix); if absent, write a top-of-file note "no image MCP — paste each prompt into nano banana pro manually" and stop after prompts (NOT a failure). `pitch-finish`: gate — refuse to finish unless `review.md` verdict = PASS. **Optional multi-reviewer finish** (adopted from FluidDocs): spawn Brand / Copy / Layout reviewer subagents in parallel over the deck+prompts; fold their fixes. If PNGs exist → bundle them with `deck.md`; else deliver `deck.md` + `prompts/`. Final checklist (≤14 slides, every slide has a prompt, review PASS, functional-pass clean if rendered). Write reusable pattern to gaspol-knowledge KB + one line to Obsidian vault `hot.md`. `ponytail:` optional editable `.pptx` via pptxgenjs is a deferred export, not built in v0.1.
4. Run test, confirm both valid.
5. Commit: "feat: add pitch-visual (per-slide prompts + functional render-pass) + pitch-finish (multi-reviewer)".
**Verification:**
- [ ] Both frontmatters valid
- [ ] pitch-visual produces one nano-banana-pro prompt per slide, citing deck-design.md; reuses carousel-gen/gaspol-design, hardcodes NO visual rules
- [ ] Exact headline + numbers baked into each prompt's TEXT section, quoted with font/size (verbatim render)
- [ ] MCP present → PNGs + functional render-pass (inspect→regen); MCP absent → prompts + manual-run note, no crash
- [ ] pitch-finish gates on pitch-review PASS; optional Brand/Copy/Layout multi-reviewer documented
- [ ] pitch-finish writes KB + vault hot.md line

### Phase H: Final integration check
**Estimated time:** 5 min
**Files:** none (validation only)
**Steps:**
1. Run full suite: manifest parses + all 7 SKILL.md frontmatters valid + all cited `references/` paths resolve + linter self-test (bad-deck trips rules, good-deck passes) + `grep -ri 'hub71\|indusia\|0F59B6\|spotlight portrait\|alisadikin' references/` returns nothing (no single-event/single-identity leakage).
2. `graphify update .` (refresh local graph) — optional.
3. Commit any fixes: "test: full gaspol-pitch integration check".
**Verification:**
- [ ] All 7 skills have valid frontmatter
- [ ] Every `references/` path cited across skills resolves on disk
- [ ] Linter self-test passes (bad fails, good passes)
- [ ] `plugin.json` valid; tree matches the design's architecture diagram

### Build order / parallelism
- Sequential spine: A → B → (C,D,E,F,G can partly parallelize once B exists) → H.
- Parallelizable after B: D, E, G skill prose are independent. C + F should be done by me (orchestrator + linter = the load-bearing logic, no delegation).
- Critical (me, never delegate per global CLAUDE.md): C (orchestrator routing), F (linter verdict/threshold logic). D/E/G prose = delegatable to `gx` executor if desired.

### Red-flag self-check
- Data Integration Map present ✅ · Verification per phase ✅ · References real files ✅ · No vague "wire up backend" ✅ · TDD-style test-first per phase (adapted to plugin asserts) ✅ · No placeholder language ✅.
