# VC Review Rubric (scored judgment)

> The **scored** complement to the binary `investor-deck-rubric.md`. Where the
> binary linter answers "does this slide pass?", this answers "how convincing is
> the deck, and where exactly is it weak?". `pitch-review` runs both.
>
> Frameworks adopted (ideas, not copyrighted): an 11-dimension scored rubric and
> "30-second retell" from the MIT-licensed cc-skills VC-fundraising skill; the
> ABC dual-test and the four Conviction Layers from public reviewer practice
> (Stevekaplan / kaustav-style reviews); cross-slide consistency from
> founder-pitch-deck-review; the earned-secret / "dinner test" from the MIT nock
> reviewer. Reworded here.

## 1. The 11-dimension scorecard (score each 1–5 → total /55)

Score 1 = absent/misleading, 3 = adequate, 5 = best-in-class. For every dimension
below 3, write the ONE concrete fix.

| # | Dimension | What a 5 looks like | What a 1–2 looks like |
|---|-----------|---------------------|------------------------|
| 1 | **Problem clarity** | A specific segment bleeds from a frequent, expensive pain | Vague "inefficiency", no one named |
| 2 | **Solution / insight** | The "aha" follows inevitably from the problem; non-obvious | Feature list, no insight |
| 3 | **Why now** | A named recent shift makes it possible *today* | Could have been built 5 years ago |
| 4 | **Market / TAM** | Bottom-up (segment × ACV), serviceable, credible | Top-down "$X trillion" |
| 5 | **Product** | Real artifact / demo / screens that work | Abstract diagram only |
| 6 | **Traction** | Compound growth, retention, real revenue | Cumulative vanity totals, flat |
| 7 | **Business model** | Clear who-pays-what-why, sane margins | "We'll figure out monetization" |
| 8 | **Unit economics** | LTV:CAC, payback, margins shown and healthy | No delivery-cost view |
| 9 | **Competition / moat** | Honest matrix + a real, compounding moat | "No competition" / "first-mover" |
| 10 | **Team / founder-fit** | Founder's past maps directly to this pain | Impressive-but-irrelevant titles |
| 11 | **The ask** | Exact $, use-of-funds %, milestones unlocked | "Raising to grow" |

**Grade bands:** 50–55 A (term-sheet ready) · 44–49 B (strong, minor fixes) ·
38–43 C (promising, real gaps) · 30–37 D (not yet) · <30 F (rebuild the thesis).
**Gate:** total < 38 OR any single dimension = 1 → BLOCKING (loop to draft).

## 2. ABC dual-test (per slide)

Every slide must do at least one of two jobs for the investor. A slide that does
neither is a cut candidate.

- **A — Reduce investor risk?** Does this slide retire a specific risk (market, product, team, timing, execution)?
- **B — Increase the value of the outcome?** Does it grow the perceived size of the win (bigger market, stronger moat, better margins)?
- **C — Neither?** → flag MERGE or CUT.

## 3. Four Conviction Layers (coverage check)

A fundable deck builds conviction on all four. Mark each Covered / Thin / Missing
and name the slide that carries it. A *Missing* layer is a structural hole.

1. **Market** — the prize is large and reachable.
2. **Insight** — the team sees something non-obvious and true (the earned secret).
3. **Founder** — this team is uniquely able to execute.
4. **Execution** — there's evidence they already are (traction, velocity, roadmap realism).

## 4. Cross-slide consistency (catches what per-slide checks miss)

Reconcile the numbers *across* the deck — investors do this in their heads and it
kills credibility when it fails.

- [ ] **TAM ↔ traction ↔ ask reconcile** — the raise + milestones are plausible given today's traction and the stated market; growth implied by financials is consistent with current run-rate.
- [ ] **Pricing ↔ unit economics** — the price on the model slide matches the LTV/CAC math.
- [ ] **AI / COGS sanity** — if the product is AI-heavy, per-unit inference/COGS is acknowledged, not hand-waved into 90% margins.
- [ ] **Headcount ↔ use-of-funds** — the hiring plan fits the raise.
- [ ] **Story continuity** — Problem → Why-now → Solution → Moat tell one coherent arc, no contradictions.

## 5. Earned-secret / insight-count gate

- The deck must carry **≥ 1 earned secret** — a non-obvious, defensible truth the team learned, across **tech / market / GTM**.
- **Earned vs faked test:** for each claimed secret ask "how did they *earn* the right to know this, and why won't a competitor see it tomorrow?" A secret with no earning story is marketing, not a moat.
- 0 earned secrets → BLOCKING. The deck describes a feature, not a company.

## 6. Narrative gates

- **30-second retell** — after one pass, can a VC retell the thesis cold to a partner? If the headlines + one demo image don't enable that, the spine is muddy.
- **Dinner test** — is there one line a partner would repeat at dinner ("they let any factory inspect parts with a phone camera")? If nothing is repeatable, nothing is memorable.

## 7. Deck-Economy (slide-count discipline)

- Target ≤ 12–15 slides. For each slide: keep / **merge** (two thin slides → one) / **cut** (fails ABC) / **split** (crams two messages).
- A deck that needs an appendix to make its case has buried its case — move the proof forward.

## Output contract (what pitch-review emits from this file)

- The /55 score table with per-dimension grade + fix for anything < 3.
- ABC verdict per slide (A / B / cut).
- Conviction-layer coverage (4 rows, Covered/Thin/Missing).
- Consistency checklist with any unreconciled number called out.
- Earned-secret verdict (count + earned-vs-faked note).
- 30s-retell + dinner-test pass/fail.
