---
name: geometry-check
description: Recompute the pixel bounding box of every node, machine, and IO block on a floor plan from its grid position, and check for overlaps or out-of-bounds placement. Use after placing or repositioning any node on a floor, when a diagram looks visually cramped or misaligned, or as part of a full factory review alongside trace-test (maths) and style-audit (conventions). This catches a class of bug neither of those checks can see, since positions are computed, not visually inspected by either.
---

# Geometry / Collision Check

Recomputes actual pixel geometry from grid coordinates and flags collisions or out-of-bounds placement. Complementary to `trace-test` (maths) and `style-audit` (style conventions) — a floor can pass both of those and still have two nodes physically overlapping on the grid.

## Reference constants (from `CLAUDE.md`)

- `FS = 154px`. Grid is 10 columns × 6 rows → valid columns 0–9, valid rows 0–5 (labelled A–F).
- Cell top-left: `left = col*154 + 20 + 52` (page padding + row label column, if computing absolute page position) or `col*154` relative to grid origin — use whichever the file's own coordinate system uses, but be consistent within one floor's check.
- Splitter/merger block: 140×140px, offset 7px into its cell (i.e. `cell_left+7, cell_top+7`).
- Machine spans (in cells, W×D): Smelter 1×2, Constructor 1×2, Assembler 2×2, Storage 1×2 — actual pixel size per the table in `CLAUDE.md`.
- IO strip blocks: 154×90px, one per column, positioned directly above (input) or below (output) the grid.

## Step 1 — Extract placements

For every node (splitter, merger, machine) on the floor, read its actual `col`/`row` (or literal pixel `left`/`top`) from the HTML/CSS. Note which representation the file uses — computed (`col*154+7`) or hardcoded pixels — since hardcoded values are themselves worth flagging (per `CLAUDE.md`, positions should be formula-derived, not hardcoded).

## Step 2 — Compute bounding boxes

For each node, compute `(left, top, right, bottom)` using its actual size (140×140 for splitter/merger, the machine-specific span for machines).

## Step 3 — Check for collisions

Compare every pair of bounding boxes on the same floor. Two boxes collide if their rectangles overlap on both axes. Report any collision with both node ids and their computed boxes.

Note: adjacent nodes sharing a cell edge (touching but not overlapping) is fine and expected — only flag genuine overlap.

## Step 4 — Check bounds

- Every node's bounding box must fall entirely within the grid (`col` 0–9, `row` 0–5, accounting for span for multi-cell machines — e.g. a 2×2 assembler at col 9 would run off the grid).
- Every port zone (47×47, at the four documented offsets within a 140×140 block) must fall within its parent node's bounding box.

## Step 5 — Check IO strip alignment and overflow

- Confirm each IO strip has exactly 10 block positions (154px each), with transparent placeholders filling any column with no active IO, so the strip stays column-aligned with the grid below/above it.
- Flag likely label overflow: if an IO block's material name or rate text is unusually long relative to the 154px block width, note it as a visual-check candidate (this is a heuristic, not a precise render check — recommend the user glance at it, don't assert it definitely overflows).

## Step 6 — Report

Per floor: list every node with its computed bounding box, then a separate list of collisions, out-of-bounds placements, and hardcoded-vs-computed inconsistencies. A clean floor has zero entries in the last two lists.
