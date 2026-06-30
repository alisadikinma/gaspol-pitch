---
name: pitch-finish
description: Final phase of gaspol-pitch. Use to package and ship the deck once pitch-review has PASSED. Gates on the review verdict, optionally runs a Brand/Copy/Layout multi-reviewer polish pass, bundles deck.md + prompts/ (+ any rendered PNGs), runs a final <=14-slide checklist, and writes the reusable pattern to the gaspol-knowledge KB plus one line to the Obsidian vault hot.md.
---

# pitch-finish

> The exit gate. Nothing ships that hasn't passed review. Then polish, bundle,
> and capture what was learned.

**Announce:** "Running pitch-finish — gating on review, polishing, and bundling the deliverable."

## HARD GATE

Refuse to finish unless `review.md` verdict = **PASS**. If it's BLOCKING, route
back to **pitch-draft** with the fix list — do not finish a failing deck.

## Step 1 — optional multi-reviewer polish

Spawn three reviewer subagents in parallel over `deck.md` (+ `prompts/`), each with
one lens, and fold their fixes:

- **Brand** — voice/tone consistency, the one motif held across slides, brand-chrome placeholder consistent.
- **Copy** — headline assertions tight, bullets ≤5/<40 words, no fluff, numbers formatted.
- **Layout** — variant fits content, type scale + grid consistent, no accent-line tell.

(Skip if the user wants a fast finish — it's a polish, not a gate.)

## Step 2 — bundle the deliverable

- If `slide-NN.png` files exist → bundle them with `deck.md` + `prompts/` + `review.md`.
- Else → deliver `deck.md` + `prompts/` + `review.md` (user renders prompts manually).
- `ponytail:` editable `.pptx` export (pptxgenjs) is a deferred upgrade, not built in v0.1.

## Step 3 — final checklist (all must hold)

- [ ] `review.md` verdict = PASS
- [ ] ≤ 14 slides
- [ ] every slide has a `prompts/slide-NN.md`
- [ ] functional render-pass clean (only if PNGs were rendered)
- [ ] brand chrome is a runtime placeholder, not a baked specific brand
- [ ] no single-event/single-identity content leaked into the deck's reusable parts

## Step 4 — capture (write-back loop)

- **gaspol-knowledge** — write the reusable pattern (what spine/structure worked for this stage/sector) to the KB.
- **obsidian** — append one high-signal line to the vault `hot.md` (the decision/insight), per the global vault rule. Vault MCP name is `obsidian-vault` (lowercase).

## Reuses (installed)

gaspol-knowledge (KB write), obsidian (vault write-back). Both already installed —
if missing at runtime, STOP and tell the user; do not skip the capture silently.

## Output

The finished bundle + a KB entry + a vault `hot.md` line.
