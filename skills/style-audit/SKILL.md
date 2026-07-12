---
name: style-audit
description: Audit a factory's existing floor plan files against the canonical design system in CLAUDE.md, to find floors still using old/legacy conventions (pre-flip port orientation, internal IO blocks, old merger styling, missing node colour system, wrong fonts, non-conforming naming) that need migrating. Use when the user wants to check, audit, or review a factory's floors for consistency, asks whether a factory "matches the current style", mentions floors being a mix of old and new, or before starting a migration/cleanup pass on a factory. This checks presentation/structure conformance, not production maths — use the trace-test skill for maths/rate correctness.
---

# Style / Layout Conformance Audit

Checks existing floor files against the canonical conventions in `CLAUDE.md`, floor by floor, to identify which are canonical, which are legacy, and which are a mix (partially migrated). This is a **read/diagnose** skill — it does not rewrite files itself. Its output is a prioritized punch list to hand to `new-floor-plan` (which does the actual editing) or to work through directly.

Read `CLAUDE.md` first for the authoritative spec. This skill is the *checklist*; `CLAUDE.md` is the *spec*. If they ever disagree, `CLAUDE.md` wins — update this checklist rather than trusting a stale copy of a rule here.

## Step 1 — Establish the reference point

There is no canonical file to compare against — `CLAUDE.md`'s written rules are the spec, full stop. Do not use "matches the newest file" as a proxy for correctness: even recently-built files can be individually wrong (e.g. `eib_floor3.html`'s IO strip placement is backwards despite postdating the port-orientation flip below, and Copper's floor files were mistakenly treated as canonical for a long stretch of the project despite predating several current conventions). Check every floor's actual markup against the rule text, on every floor, regardless of its age.

Known legacy markers to treat as definitely-old without further checking:
- `iron_megafactory.html` (pre-rebuild), `steel_eib_factory.html` — legacy layout style.
- Any LB file older than `eib_lb3b.html` / `eib_floor2.html` — predates the port-orientation flip.

These markers only tell you a file is *likely* to fail; they don't tell you a file that postdates them is safe. Run the full checklist below on every floor either way.

**Include `templates/lb-floor-template.html`, `templates/production-floor-template.html`, and `templates/main-factory-page-template.html` in scope whenever you run a full-repo or cross-factory audit** (not required for a single-factory audit that doesn't touch them). Since every new floor is meant to start from these, a drifted template silently propagates its defect into everything built from it afterward — this is the mechanism that's supposed to catch that before it happens. If a template fails a check, that's higher priority to fix than any individual built floor.

## Step 2 — Per-floor checklist

For every floor file in the target factory, check each item below. Record **pass / fail / partial** with the specific evidence (element, attribute, or line) for anything that fails.

### IO placement
- [ ] All IO (input/output) rendered as **external strips** above/below the grid, not as blocks inside the grid itself. 1540px wide, 154px per column, placeholders for empty columns.
- [ ] **Strip position matches floor type** — this is the single most commonly-violated rule, check it explicitly rather than skimming: LB sub-floor = input **above** grid, output **below**. Normal production floor = input **below** grid, output **above**. (Opposite of each other — don't assume one convention applies to both floor types.)
- [ ] **Arrow direction matches floor type**: LB sub-floor = every direction arrow (input strip and output strip alike) points **down**. Production floor = every direction arrow points **up**. A floor with mixed arrow directions between its own input and output strips is always wrong, regardless of floor type.
- [ ] IO block structure: direction/lift label, material+rate (Barlow Condensed 13px), source/dest node ref coloured by that node, floor destination muted (opacity 0.6).

### Splitter/merger nodes
- [ ] Blocks are 140×140px, centred in a 154px cell (7px offset each side), not some other size/offset.
- [ ] Port zones are 47×47px at the four documented positions (centre 47,47 / top 47,0 / bottom 47,93 / left 0,47 / right 93,47).
- [ ] **Port orientation (post-flip)**: splitter input on the **top** port; merger output on the **bottom** port. This is the single most common legacy defect — check it explicitly on every node, not just spot-check.
- [ ] Port label format is `NODE / rate` (e.g. `MRG1 / 15/m`) — no arrows, no "in"/"out" text. Legacy files may use arrow characters or directional words here.
- [ ] Splitter styling: material background + solid material border.
- [ ] Merger styling: fixed dark teal `#0a1a1a` background + `2px dashed #1a5a5a` border, **regardless of material**. Legacy files may colour mergers by material instead — flag this.
- [ ] Naming: `SPL1`, `SPL2`, `MRG1`... uppercase, no space, numbered from 1. Combined-network floors prefix with network id.

### Node colour system
- [ ] Each node has a unique CSS colour variable declared in `:root`.
- [ ] Every port label referencing a node is coloured with that node's variable (not a fixed/generic colour).
- [ ] Terminal ports (no node on the other end) use material colour, not a node colour.
- [ ] A node colour key panel exists below the diagram, listing every node with its swatch.
- [ ] Spot-check: pick 2–3 port labels, confirm their colour matches exactly one node's own centre-label colour. Mismatches or colours with no owning node are defects.

### Machine nodes
- [ ] Machine sizes match the table in `CLAUDE.md` (Smelter/Constructor/Assembler/Storage).
- [ ] Input tags present, one pill per ingredient, formatted as coloured pills.
- [ ] Same-floor machine-to-machine feeds use internal feed tags, not input-strip entries.

### Fonts
- [ ] Font family + size for each element type matches the table in `CLAUDE.md` (diagram labels, machine labels, page chrome are three different scales — check each).

### Page structure
- [ ] Nav bar present with prev/next, full floor quick-jump (current highlighted), factory link; last floor shows "— top floor —".
- [ ] LB floors have: legend, node colour key panel, notes section, belt warning box if any trunk is near tier cap.

## Step 3 — Classify each floor

- **Canonical** — passes all checks.
- **Legacy** — fails most/all checks, clearly predates the current system.
- **Mixed** — some sections updated, others not (e.g. IO strips externalized but port orientation still pre-flip). Flag mixed floors as highest priority to finish — half-migrated files are the easiest to end up inconsistent within themselves.

## Step 4 — Report

Produce a per-factory table: floor file → classification → specific failed checks. Then group failures across the whole factory by **defect type** rather than by floor — if "merger styling coloured by material" appears on 4 floors, that's one systematic fix to make across 4 files, not 4 separate problems. This grouping is what should drive the actual migration work order (fix systematic issues first, across all affected files, per `CLAUDE.md`'s rule that systematic defects are never patched in isolation).

Do not silently fix anything while auditing. If a fix is genuinely ambiguous (e.g. unclear which node a stray-coloured port label was meant to reference), report it as needing resolution rather than guessing.

## Note on scope

This audit does not check production maths, rate conservation, or handoffs between floors — that's `skills/trace-test/SKILL.md`. A floor can be style-canonical and maths-broken, or maths-correct and style-legacy; run both audits when doing a full factory review.
