---
name: cross-reference-propagation
description: Find and update every file in the repo that references a rate, node name, material, or IO routing that just changed on some floor. Use immediately after editing any floor's rates, IO strips, node names, or routing — before considering the change done — and whenever the user mentions stale references, things being out of sync, or asks "did I miss updating anything." This closes the gap where a floor gets fixed but adjacent floors, the factory summary page, or index.html still show the old value.
---

# Cross-Reference Propagation

A single-purpose skill: given something that just changed on one floor, find every other place in the repo that needs the matching update. This is diagnostic/edit — unlike `trace-test` and `style-audit`, it's expected to be run as part of finishing an edit, not as a separate audit pass.

## Step 1 — Define exactly what changed

Be precise before searching. "The rate changed" isn't enough — capture:
- Old value(s): rate, node id, material name, or routing description.
- New value(s).
- The file and location where the source-of-truth change was made.

If a node was renamed (e.g. `SPL3` → `SPL4` because of a reordering), track both the id change and any rate change together — renames without rate changes still need every reference updated.

## Step 2 — Search known reference locations

Check each of these categories explicitly — don't rely on a single grep catching everything, since some references are prose (factory chain breakdown) and won't match a rigid pattern:

1. **Adjacent floors' IO strips** — the floor above/below whose input or output strip should mirror this change (per the handoff rule in `CLAUDE.md`).
2. **Main factory page**:
   - Build summary bar: belt requirements, floor heights, clock speeds, buildings required.
   - Floor plans grid card (if height, name, or primary material changed).
   - Production chain breakdown prose and any inline LB-diagram divs referencing this node/rate.
3. **`index.html`** — factory card stats (outputs, machine count) if the change affects factory-level totals.
4. **Node colour key panels** — if a node was renamed or added/removed, the key panel on its own floor and any cross-floor colour reference must match.
5. **Nav bar** — if a floor was renamed or reordered, every other floor's nav bar quick-jump list needs the same update (this is usually every file in the factory, so use the propagation script pattern, not manual edits).
6. **Belt warning boxes** — if a rate changed near a belt tier cap, re-check whether the warning still applies, needs a different tier called out, or should be removed.

Use `grep`/full-text search for the literal old value (rate number, node id, material name) across the factory's files as a first pass, then manually check the prose locations in category 2 that a literal-string search may phrase differently.

## Step 3 — Classify each match

For every file where the old value appears:
- **Needs update** — this is a stale reference to the changed thing.
- **Coincidental match** — same number/string, unrelated context (e.g. a different node's rate happens to also be 15/min). Do not touch.
- **Unclear** — can't tell from context; flag for the user rather than guessing.

## Step 4 — Propagate

Use targeted string replacement (regex matching structural patterns — e.g. adjacent nav-button pairs, a specific `.io-block` matching the old node id — not line numbers, which shift between files) so the same edit can be applied consistently across every affected file.

## Step 5 — Close the loop

After propagating, re-run whichever of `trace-test` or `style-audit` is relevant to what changed (a rate change → trace-test; a renamed node's colour/label → both) to confirm the propagation didn't introduce a new inconsistency.
