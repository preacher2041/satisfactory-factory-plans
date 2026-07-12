---
name: new-material-onboarding
description: Add a new material to the project's colour system and conventions when a factory chain introduces a material that doesn't yet have a documented colour (e.g. starting Rubber production, or a new intermediate product). Use whenever a production chain design references a material not already in the CLAUDE.md colour table, before it appears in any HTML. Prevents accidental colour collisions and ensures the material is wired into every system that depends on colour (IO strips, machine tags, node colours if used on LB floors).
---

# New Material Onboarding

A short, deliberate checklist for introducing a new material colour — small in scope, but easy to do carelessly (an accidental colour collision reads as a real ambiguity later, unlike the deliberate ones already documented).

## Step 1 — Check for an existing colour first

Search `CLAUDE.md`'s material colour table for the new material — it may already be listed even if not yet used in built HTML (the table covers the full known production chain, not just what's built so far). If it's already there, use that colour; skip the rest of this skill.

## Step 2 — Check for collisions

Compare the proposed new colour against every existing entry in the table.

- **Unintentional collision** (same or near-identical hex as an unrelated material): pick a different colour.
- **Intentional reuse** (like cable/steel pipes sharing green, or copper sheet/concrete/modular frames sharing purple): only do this deliberately, and only when the materials are unlikely to appear as ambiguous labels in the same context — confirm with the user before reusing an existing hex rather than assuming it's fine.

Prefer picking from a visually distinct hue against materials that appear physically adjacent in the same production chain (e.g. a material and its own raw ore, per the existing ore/ingot distinction rule) — desaturated vs. saturated, or a different hue family entirely.

## Step 3 — Add the colour

1. Add the material + hex to the table in `CLAUDE.md`, in the same two-column format, alongside a one-line note if this is an intentional colour reuse (so the reason is documented, not just the fact).
2. If the material is new enough to need explaining (e.g. why it's distinct from a similarly-named material), add the same kind of clarifying note the ore/ingot pairs have.

## Step 4 — Check downstream systems that depend on colour

The colour needs to actually get used, not just documented. Check whether the material will appear in:

- **IO strip blocks** — background/border styled from the material colour.
- **Machine input tags** — pill colour for any ingredient tag using this material.
- **Node colour system** — only relevant if this material's belts run through a splitter/merger network; node colours are independent of material colour (per `CLAUDE.md`), so no change needed there beyond making sure the material colour isn't confused with a node colour in the same diagram.
- **Floor plan legend / production chain breakdown** — if the main factory page's chain breakdown references this material, it should use the new colour consistently there too.

## Step 5 — Confirm before building

Once the colour is added to `CLAUDE.md`, treat it as settled — don't let individual floor files pick their own shade for the same material later. If a floor file is later found using a different hex for the same material, that's a defect for `style-audit` to catch, not a new colour choice to make on the spot.
