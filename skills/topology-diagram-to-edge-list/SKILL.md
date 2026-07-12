---
name: topology-diagram-to-edge-list
description: Turn a topology diagram (from docs/topology_diagrams/ or supplied directly) into a locked, node-by-node edge list, resolving ambiguous crossing edges or unclear splitter/merger shapes collaboratively rather than guessing. Use whenever starting work from a diagram, before any HTML or even maths verification begins. This is the first step of designing a new floor or factory, and can also be run standalone to just get a clean edge list without committing to building anything yet.
---

# Topology Diagram → Edge List

Converts a visual topology diagram into a precise, text-form edge list that `production-maths` and `new-floor-plan` can build on. This is purely about correctly reading the diagram — no rate calculation, no HTML.

## Step 1 — Locate the diagram

Check `docs/topology_diagrams/<factory>/` for a file matching the floor(s) in question before asking the user to supply one directly.

## Step 2 — Assess parseability

- **Clean tree structure, no crossing edges**: can usually be parsed directly and confidently — proceed to Step 3.
- **Lateral flows, crossing edges, or ambiguous node shapes** (can't reliably distinguish a splitter diamond from a merger square at small size with crowded edges): do not guess through this. Go to Step 4 instead.

Node *positions* in the diagram carry no floor-plan layout meaning — layout is a separate decision made later in `new-floor-plan`. Don't let visual position influence the extracted topology.

## Step 3 — Direct extraction

For each node in the diagram, record:
- Node type (splitter / merger / machine / terminal input / terminal output).
- All incoming edges (source, material, rate if labelled).
- All outgoing edges (destination, material, rate if labelled).

Produce the edge list (format in Step 5) and present it for confirmation before treating it as locked — even a "clean" diagram read should get a quick confirm, since a single misread edge propagates into every downstream calculation.

## Step 4 — Collaborative disambiguation

When the diagram isn't cleanly parseable:

1. State plainly what's confidently extracted — don't hedge on the parts that are clear.
2. List each specific ambiguity: which node, what's unclear about it (shape, an edge that crosses others, a label that's hard to read), and what the candidate readings are.
3. Ask the user to resolve each ambiguity directly rather than proposing a "most likely" guess as the default — this is exactly the case flagged in `CLAUDE.md` as one where behavioral arithmetic (flow conservation), not visual guessing, should be the tiebreaker once rates are known.
4. If conservation maths can resolve an ambiguity on its own (e.g. two candidate readings, only one balances), say so explicitly and use it — but still confirm with the user before locking it in, since a diagram error elsewhere could make the "balancing" reading wrong too.

## Step 5 — Edge list format

Once confirmed, produce a locked edge list:

```
NODE: <id> (<type: splitter|merger|machine|terminal>)
  IN:  <source id or "external"> — <material> — <rate or "unlabelled">
  OUT: <dest id or "external"> — <material> — <rate or "unlabelled">
  ...
```

List every node, in any order, but every edge must appear exactly once from each end (i.e. node A's OUT to node B should match node B's IN from node A).

## Step 6 — Hand off

Once locked, this edge list is the input to `production-maths` (to fill in/verify rates and check splitter validity) and then `new-floor-plan` (to decide grid layout and build the HTML). Don't let the diagram itself be re-consulted for details later — the locked edge list is now the source of truth, so any correction to it should be an explicit, noted revision, not a silent re-read of the image.
