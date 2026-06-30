# Unit Economics — formulas + healthy thresholds

> Brain for the financials/unit-econ slide and for `pitch-review`'s signal pass.
> Thresholds are **rules of thumb — verify per stage and sector**; tagged where
> volatile. Public SaaS/VC benchmarks, summarized.

## Core formulas

| Metric | Formula | Note |
|--------|---------|------|
| **CAC** | total S&M ÷ new customers (same period) | Blend paid + organic, or split them |
| **LTV** | ARPA × gross-margin % ÷ churn rate | Use gross-margin LTV, not revenue LTV |
| **LTV:CAC** | LTV ÷ CAC | The headline efficiency ratio |
| **CAC payback** | CAC ÷ (ARPA × gross-margin %) | Months to recoup acquisition cost |
| **Gross margin** | (revenue − COGS) ÷ revenue | COGS includes hosting/inference/support |
| **Contribution margin** | revenue − all variable cost (incl. CAC amortized) | Per-unit profitability |
| **NRR (net revenue retention)** | (start ARR + expansion − churn − contraction) ÷ start ARR | Expansion engine |
| **Burn multiple** | net burn ÷ net new ARR | Capital efficiency of growth |
| **Rule of 40** | growth % + profit (or FCF) margin % | Balance of growth vs profitability |
| **Magic number** | net new ARR ÷ prior-quarter S&M | Sales efficiency |

## Healthy thresholds (rules of thumb — verify)

| Metric | Healthy | Great | Red flag |
|--------|---------|-------|----------|
| **LTV:CAC** | ≥ 3× | ≥ 5× | < 3× (you buy customers at a loss-ish) |
| **CAC payback** | < 12 mo | < 6 mo | > 18 mo (early-stage), > 24 (any) |
| **Gross margin (SaaS)** | 70–80% | > 80% | < 60% without a reason |
| **NRR** | > 100% | > 120% | < 90% (leaky bucket) |
| **Burn multiple** | < 2 | < 1 | > 3 (inefficient growth) |
| **Rule of 40** | ≥ 40 | ≥ 60 | < 20 |
| **Magic number** | > 0.75 | > 1.0 | < 0.5 (sales not paying back) |
| **Growth (early SaaS)** | ~2× YoY | 3×→2×→2× ("T2D3") | flat / linear |

## Margin profiles by model (verify per sector)

- **Pure SaaS** — 75–85% GM; LTV:CAC and payback dominate the story.
- **Marketplace** — report take-rate AND GMV; "GM" is on the rake, not GMV.
- **Hardware + software** — hardware GM thin (20–40%); lead with the software attach + recurring margin.
- **Transactional / fintech** — GM net of payment + risk cost; show contribution after losses.
- **AI-native** — state per-unit inference COGS explicitly; margins compress with usage if not managed. Do NOT claim pure-SaaS margins on a token-metered product.

## What `pitch-review` checks against this file

- **Hard gates:** LTV:CAC ≥ 3× AND CAC payback < 12 mo (or an explicit, credible path to both).
- TAM is **bottom-up** (serviceable segment × ACV), not top-down.
- Growth is venture-scale (≥ ~2× YoY early), not linear.
- For the model type, the right metric is shown (NRR for SaaS, take-rate+GMV for marketplace, SW-attach for hardware).
- AI products acknowledge inference COGS — flag any 90% margin claim on a metered AI product as a consistency failure.
