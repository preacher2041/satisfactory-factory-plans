---
name: new-floor-plan
description: Build a new factory main page, production floor, or LB (load-balancer) sub-floor for the Satisfactory documentation project. Use whenever the user wants to add a new floor, a new factory, a new LB sub-floor diagram, or asks to document a production chain that doesn't have HTML yet. Also use when extending or rebuilding an existing floor (e.g. "rebuild the ingot split floor") since the same conventions and checks apply. Always consult this skill before writing any factory HTML — the grid math, port rules, and colour system are easy to get subtly wrong from memory alone.
---

# New Floor Plan / Factory Page

Builds Satisfactory factory documentation pages that conform to the conventions in the repo's `CLAUDE.md`. Read `CLAUDE.md` first — it holds the grid constants, font table, colour palette, and node-colour rules referenced below. This skill is the *procedure*; `CLAUDE.md` is the *spec*.

## Step 0 — Start from the template, find the topology source

Before designing anything:
1. `docs/topology_diagrams/<factory>/` for a source diagram matching the floor(s) being built — check this before asking the user to paste one in chat.
2. Copy the matching file from `templates/`: `templates/lb-floor-template.html` for an LB sub-floor, `templates/production-floor-template.html` for a production floor, `templates/main-factory-page-template.html` for a new factory's main page. These are worked examples (openable, valid HTML, not fill-in-the-blank skeletons) that embody the current rules in `CLAUDE.md` — read the `TEMPLATE:` comments at the top of whichever one you use, they list exactly what to replace.

Do not copy structural conventions from any built factory file instead of the template — even recently-built files can be individually wrong (e.g. `eib_floor3.html`'s IO strip placement is backwards despite being built after the port-orientation flip). If you reference a built file anyway (for inspiration on a specific pattern like a combined-network LB floor), **verify every convention against `CLAUDE.md`'s written rules** rather than assuming the file itself is current.

Do not copy from `iron_megafactory.html` (pre-rebuild) or `steel_eib_factory.html` structure/prose conventions at all — they're legacy layout, kept only as historical reference.

If the templates themselves ever seem to disagree with `CLAUDE.md`'s written rules, the rules win — flag the template as needing an update (see the "Keeping templates in sync" note in `CLAUDE.md`'s Repo map) rather than propagating the mismatch into a new floor.

## Step 1 — Design the production chain (no HTML yet)

Work this out in chat/plain text first, and get explicit confirmation before touching HTML if any of the following are non-trivial:

1. **Machine counts, clock speeds, belt rates, routing.**
2. **Per-machine consumption**: `recipe ingredient qty × machine output rate` (accounting for clock speed) — never per-item quantities treated as per-minute figures.
3. **Conservation check**: produced = consumed + surplus, at every node and at floor level, including any recirculation.
4. **Splitter validity**: every splitter output must be an exact equal split (halves for 2-way, thirds for 3-way). If the target split is uneven, either adjust the input rate to make it clean, or design an explicit splitter+merger network — never approximate.
5. **Belt tier check**: every trunk rate against Mk.1/2/3/4 caps (60/120/270/480 per min).
6. **Manifold-first**: per player preference, prefer a manifold-compatible belt speed over a load-balancer network unless manifolds are genuinely unworkable for the split required.

**Reading topology diagrams** (from `docs/topology_diagrams/` or supplied directly by the user): clean tree diagrams with no crossing edges can usually be parsed directly. Diagrams with lateral flows, crossing edges, or ambiguous splitter-vs-merger shapes should not be guessed through — state what's confidently extracted, flag the ambiguous parts, and resolve them with the user before writing code. Node *positions* in a topology diagram carry no floor-plan layout meaning — layout is decided independently in Step 3.

Lock the full node-by-node edge list and confirm the maths closes before proceeding.

## Step 2 — Main factory page (new factories only)

Structure, in order (see `copper_factory.html` or `iron_megafactory.html` for worked examples — verify against the structure below rather than assuming either is current):

1. **Header** — `<h1>` factory name, sub-title, back link to `index.html`.
2. **Build summary bar** — `display:grid; grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));`, bordered cells, no sticky positioning. Five sections: Factory (footprint/height/foundations/machine count/power shards), Floor heights, Belt requirements, Clock speeds, Buildings required. Reuse `.summary-section-title`, `.summary-row`, `.summary-key`, `.summary-val`, `.belt-chip`.
3. **Floor plans grid** — `repeat(auto-fill, minmax(160px, 1fr))` cards linking to each floor file. Production floors: `var(--surface2)` background. LB sub-floors: `#0d1018` background. Left border colour-coded by primary material.
4. **Production chain breakdown** — full width, `.chain-section`/`.chain-header`/`.chain-grid`/`.chain-card`. Covers recipes, throughput maths, clock-speed reasoning, LB/manifold notes, outputs. LB diagrams here use plain HTML divs in prose form (never SVG) — reference the node names/colours consistently with the floor plan files.

Same theme/fonts (per `CLAUDE.md`'s Fonts table) and CSS var naming pattern (`--material`, `--material-bg`, `--material-bd`) as every other factory page. Single full-width column, no side panels. Add any new material colour as a CSS variable in `:root`. When done, add a card to `index.html`.

## Step 3 — Floor plan files

**Production floor**: grid holds machines only. External IO strips: input **below** the grid, output **above** — all direction arrows point **up**, per `CLAUDE.md`'s External IO strips table. No IO blocks inside the grid.

**LB sub-floor**: grid holds splitter/merger nodes only. External IO strips: input **above** the grid, output **below** — all direction arrows point **down**.
- Compute all positions from `col*154+7`, `row*154+7` — never hardcode pixel offsets.
- Splitters in upper rows (near input strip), mergers in lower rows (near output strip), reflecting top-to-bottom flow. Leave row A / row F as breathing room where topology allows.
- Apply port placement and node-colour rules exactly as specified in `CLAUDE.md` (post-flip convention: splitter input = top port, merger output = bottom port).
- **Pass-through LB floor** (no splitting/merging, just routing belts across): grid stays empty, only IO strips shown aligned to entry/exit columns, plus a note explaining the routing.
- **Combined-network LB floor** (two+ independent material networks on one floor): each network in its own columns, vertical divider line at the boundary, separate node colour palettes, both networks listed separately in the node colour key.
- Every LB floor needs: legend (splitter/merger swatches), node colour key panel, notes for non-obvious routing, belt warning box if any trunk is at/near tier cap.

**Machine input tags** on every machine node: coloured pills, one per ingredient, `recipe qty × actual output rate`. Same-floor machine-to-machine feeds get an internal feed tag instead of an input-strip entry.

**Nav bar** on every floor file: prev/next, all-floor quick jump (current highlighted), factory link. Last floor shows "— top floor —" instead of next. Order follows Ground → up.

## Step 4 — Verify

Do not consider the floor(s) done until they pass the trace test — see `skills/trace-test/SKILL.md`. If a dedicated trace-test subagent is available, prefer running it over self-checking, since an independent pass catches more than the same context checking its own work.

## Step 5 — Propagate

After any structural change (new floor, changed IO strip, changed rate), check and update:
- Cross-references in adjacent floor files (their IO strips must still match this floor's).
- The main factory page's summary bar, floor plan grid, and production chain breakdown.
- `index.html` card for this factory (status/outputs/stats).

Use targeted string replacement (regex matching structural patterns like adjacent button pairs, not line numbers — they shift between files) for propagating the same edit across multiple files.
