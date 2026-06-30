# Business Model Canvas + Revenue Models + Pricing

> Brain for the business-model slide (read by `pitch-draft`, sanity-checked by
> `pitch-review`). Public frameworks (Osterwalder's BMC, standard revenue-model
> taxonomy, van Westendorp PSM), summarized as a checklist.

## Business Model Canvas — 9 blocks

Each block = a one-line definition + the question the slide must answer.

| Block | Definition | Question to answer on the deck |
|-------|-----------|--------------------------------|
| **Customer Segments** | Who you serve | Which specific segment first (beachhead), and who later? |
| **Value Proposition** | The job you do better | Why is the outcome 10x, not 10%? |
| **Channels** | How you reach + deliver | What's the repeatable path to the customer? |
| **Customer Relationships** | How you keep them | Self-serve, sales-assisted, or high-touch? |
| **Revenue Streams** | How you capture value | Who pays, how much, how often? |
| **Key Resources** | What you must own | Data, IP, network, brand — the moat source |
| **Key Activities** | What you must do well | The few things that must work |
| **Key Partnerships** | Who you depend on | Where's the platform/supply risk? |
| **Cost Structure** | What it costs to deliver | Fixed vs variable; where's COGS? |

A coherent model = these 9 reinforce each other. `pitch-review` flags any block
that contradicts another (e.g. self-serve channel but high-touch cost structure).

## Revenue-model taxonomy

| Model | How it bills | Typical gross margin | When to use |
|-------|-------------|----------------------|-------------|
| **Subscription (SaaS)** | Recurring per seat/tier | 70–85% | Repeated value, low marginal cost |
| **Usage / consumption** | Per API call, GB, transaction | 50–80% (watch COGS) | Value scales with use; AI/infra |
| **Transaction fee** | Cut per transaction | 60–90% | You sit in a payment/exchange flow |
| **Marketplace take-rate** | % of GMV | high on rake, low on GMV | Two-sided network; report take-rate AND GMV |
| **Licensing / IP** | Per-deployment / royalty | very high | Defensible IP, few large buyers |
| **Freemium** | Free → paid conversion | depends on conversion | Bottom-up, viral; report free→paid % |
| **Hardware + software** | One-time + recurring | hardware thin, SW fat | Show the SW attach + recurring layer |

> Margin numbers are rules of thumb — verify per sector/stage. AI products: state
> per-unit inference COGS; don't assume pure-SaaS margins.

## Pricing methods

- **Cost-plus** — markup over cost. Easy, but leaves value on the table; weakest for a venture story.
- **Value-based** — price to a fraction of the value delivered (the dream outcome). Strongest; ties price to the Value Equation.
- **Van Westendorp PSM** — survey 4 price points (too cheap / cheap / expensive / too expensive) to find the acceptable band. Use to defend a price empirically.
- **Anchor + tiers** — a high anchor tier makes the target tier feel reasonable; 3 tiers is the default.

## What the deck must show

1. Who pays, how much, how often (the revenue stream).
2. That the price is consistent with the unit economics (see `unit-economics.md`).
3. A reason the model gets *better* with scale (margin expansion, NRR, network).
