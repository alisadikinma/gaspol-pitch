# Investor Pitch-Deck Rubric (binary linter)

> The **binary** brain for `pitch-review`. Synthesis of public investor guidance
> (YC + Sequoia + Barbara Minto's Pyramid Principle). Each check is pass/fail —
> no scoring here (scoring lives in `vc-review-rubric.md`). Prioritize concrete
> thresholds over generic advice.

## PART A — Slide Sequence (canonical order)

Each slide = ONE job + the failure that makes a VC lose interest.

1. **Title / Company purpose** — one-line "what we do" a stranger grasps instantly. *Fail:* vague tagline, no category anchor.
2. **Problem** — a painful, expensive, frequent problem for a real segment. *Fail:* invented/niche problem, no one bleeds from it.
3. **Solution / Value prop** — how you remove the pain, the "aha". *Fail:* feature list instead of outcome.
4. **Why Now** — the recent tech/regulatory shift that makes this possible *today*. *Fail:* no inevitability; could've been built 5 yrs ago.
5. **Market / TAM** — bottom-up sizing of the prize. *Fail:* top-down "$X trillion" from a generic report.
6. **Product** — show it working (demo/screens/flow). *Fail:* abstract diagrams, no real artifact.
7. **Traction** — proof of pull (revenue, growth, retention). *Fail:* cumulative vanity users, flat growth.
8. **Competition** — matrix/quadrant showing defensible differentiation. *Fail:* arrogantly claiming "no competition".
9. **Team** — why *this* team uniquely executes. *Fail:* impressive-but-irrelevant titles, no photos, no founder-market-fit link.
10. **Financials & Roadmap** — realistic 3-yr P&L + milestones for next round. *Fail:* flat/linear growth, not venture-scale.
11. **The Ask / Use of Funds** — exact raise, % breakdown, time-bound milestones unlocked. *Fail:* "raising $2M to grow".

## PART B — Investor Signals (what makes a VC believe "this is the best")

| Signal | STRONG | WEAK | Required proof |
|---|---|---|---|
| **Market / TAM** | Bottom-up: serviceable × ACV, capturable in 12–24mo | Top-down generic industry reports | ACV from real pricing tiers × validated segment |
| **Traction / Growth** | Compound monthly growth, repeatable organic channels | Flat growth, cumulative "total users" | Time-bound cohort retention OR "2x QoQ" velocity |
| **Team / Founder-mkt fit** | Founder's past directly maps to the pain | Generic corporate titles, no domain depth | One-line credentials linking past achievement → this problem |
| **Moat / Defensibility** | Switching costs, proprietary data, network effects | "10x better", unproven "first-mover" | Quadrant vs named direct + indirect competitors |
| **Business model / Unit econ** | Sustainable CAC, fast breakeven | Generic price tiers, no delivery-cost view | **LTV ≥ 3× CAC** AND **CAC payback < 12 months** |
| **The Ask & Use of funds** | Exact target + operational roadmap | "Raising $2M to grow" | Exact $, % split (eng/sales/ops), time-bound milestone (e.g. target ARR) |

## PART C — Adversarial Review Checklist (linter; each slide pass/fail)

These are the 9 binary checks `pitch-review` runs against every slide.

- [ ] **Value-Anchor** — can reviewer extract capability + product + user + scenario + problem without guessing?
- [ ] **Pyramid Principle** — slide leads with conclusion at top → evidence → granular metric at bottom?
- [ ] **Assertion vs Topic** — headline is an active argument ("Market is a $XB opportunity") not a label ("Market Size")?
- [ ] **One Message** — slide crams two ideas via "and"? If yes → FAIL, split into two slides.
- [ ] **Linguistic Fluff** — banned VC-slop (synergy, leverage, paradigm shift, disruptive, world-class, revolutionize, cutting-edge, best-in-class)? If yes → FAIL.
- [ ] **5-Second Rule** — core point grasped in 5s with no clutter?
- [ ] **Data Parsing** — numbers strictly formatted ("$2M", "15%", "4.1x"), no "four million", no junk Unicode?
- [ ] **Platform Risk** — over-reliant on one closed API with no declared fail-safe?
- [ ] **Forwardable Test** — associate forwards deck to partner with ZERO verbal context: does the narrative stand alone?

## PART D — Narrative & Storytelling (wins term sheets)

- **Traction-first sequencing** — if early metrics are exceptional, move Traction to the front. VCs scan 30–60s; lead with proof.
- **"Why Now" inevitability** — pin the exact recent trend (tech/regulatory) that makes this possible *right now*.
- **Headline rule** — reading ONLY the slide headlines must reconstruct the full investment thesis. Headlines = complete sentences, max 2 lines, insight front-loaded.
- **Bullet discipline** — each bullet = mini-argument (assertion → evidence). Max 3–5 per slide, <40 words each, active voice.
- **Contextualized numbers** — never a floating number; always benchmark against a baseline to show trajectory.
