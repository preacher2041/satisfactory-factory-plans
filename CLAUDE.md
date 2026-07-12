# Satisfactory Factory Documentation

Interactive HTML floor-plan documentation for Satisfactory 1.0 factories. Three factories: **Iron Megafactory**, **Steel & EIB Factory**, **Copper Factory**. No single factory's files are the canonical reference — **the written rules in this document are authoritative**. Built files drift out of date as the project iterates (e.g. Copper's floor files predate several conventions below and should not be copied from without checking them against this doc first); treat any existing file as an implementation example to verify, never as a source of truth in its own right.

## Game context

- Satisfactory 1.0, Phase 3 in progress (Phases 1–2 complete). Tiers 0–6 unlocked.
- EIB factory: built and running. Modular Frames: in progress. Rubber: not started (no oil processing yet).
- Running production: Copper Sheets/Wire/Cables; Concrete 30/min; Iron Plates/Screws/Rods 60/min; Rotors, Steel Beams, Steel Pipes; Reinforced Iron Plates 5/min; Smart Plating; EIB 30/min.

## Player preferences

- **Manifolds over load balancers** — always try a manifold-compatible belt speed first; only use a splitter/merger LB network if manifold is genuinely unworkable.
- Overclock miners rather than duplicate them (power shards available). Miner Mk.2.
- Multi-storey, uniform: 8m×8m foundations, 12m floor height (3 wall stacks), consistent across all floors.
- Belt lifts on building exterior. Storage on ground floor.

## Repo map

- `docs/topology_diagrams/<factory>/<floor-name>.*` — source topology diagrams, one folder per factory, files named for the floor(s) they apply to. **Check here first** before asking the user to paste a diagram — a file may already exist for the floor being built or rebuilt.
- `templates/lb-floor-template.html`, `templates/production-floor-template.html`, `templates/main-factory-page-template.html` — **start every new floor or factory page here.** These are minimal worked examples (openable, valid HTML with a small real network — not fill-in-the-blank skeletons) that embody the current rules in this document, with `TEMPLATE:` comments marking what to replace. They exist specifically because no built factory's files stayed a reliable reference as the project iterated — see below.
- `index.html` — master factory index. Every new factory gets a card here (name, status, outputs, footprint, machine count, link).
- `iron_megafactory.html` (pre-rebuild), `steel_eib_factory.html` — legacy single-diagram layout, kept only as historical reference. Do not copy structural conventions from these.
- No built factory's floor files are a safe template to copy blind — even recently-built files can contain isolated mistakes (e.g. `eib_floor3.html`'s IO strip placement is backwards despite postdating the port-orientation flip below). If you reference one anyway (e.g. for a specific pattern like a combined-network LB floor not covered by the templates), **verify every convention it uses (IO strip placement, port orientation, naming, colours) against the rules in this document** — don't assume a file is correct just because it's recent.

**Keeping the templates in sync**: whenever a convention in this document changes (grid math, colour system, IO strip rules, fonts, anything), updating the matching template file(s) is part of that same change — not a follow-up task. `style-audit` also audits the templates themselves (not just built factories) so drift gets caught by the normal verification pass. If a template and this document ever disagree, this document wins; fix the template.

Port label orientation was flipped after `eib_lb3b.html`/`eib_floor2.html` were built — anything older uses the pre-flip convention (splitter input on bottom port) and should not be copied. This is a historical note about when the rule changed, not a claim that files after that point are reliably correct — always check against the rule itself (see Node colour system, below).

## Grid system (floor plan files)

- Viewport 1665×1000px. `FS = 154px` per foundation (8m×8m). Grid = 10 cols × 6 rows = 1540×924px.
- Page padding 20px top/bottom, 24px sides. Row label column 52px. Total width 52+1540+48 = 1640px.
- Coordinates are **computed**, never hardcoded: `col*154+7`, `row*154+7`.
- Splitter/merger block: 140×140px, centred in cell (7px offset each side). Port zone: 47×47px.
- Machine sizes @ FS=154: Smelter 116×232 (1W×2D), Constructor 128×232 (1W×2D), Assembler 256×256 (2W×2D), Storage 96×232 (1W×2D), Foundry 256×256 (2W×2D).
- Foundry is 2W×2D, not 1W×2D — its real in-game width (10m) exceeds a single 8m foundation column, same as Assembler's (9m), which is why Assembler is the only other 2W entry above. Foundry reuses Assembler's 256×256 box rather than a new size; its real length (9m) fits comfortably within that depth allowance. (Style-audit found `eib_floor1.html`'s foundries built at the Smelter's 1W×2D footprint — this is the corrected size to migrate them to.)

## Fonts

| Element | Font | Size |
|---|---|---|
| Splitter/merger centre label | Share Tech Mono | 13px |
| Splitter/merger port label | Share Tech Mono | 11px |
| IO block material name | Barlow Condensed | 13px |
| IO block rate | Share Tech Mono | 13px |
| IO block source/dest | Share Tech Mono | 10px |
| Machine name (.m-lbl) | Barlow Condensed | 15px |
| Machine sub-label (.m-sub) | Share Tech Mono | 12px |
| Input tags | Share Tech Mono | 10px |
| Page h1 | Barlow Condensed | 24px |
| Sub-title / nav / axis labels / legend | Share Tech Mono | 11px |
| Notes | Share Tech Mono | 12px |
| Belt warning box | Share Tech Mono | 13px |

## External IO strips

All inputs/outputs on every floor (production or LB) live in **external strips outside the grid**. The grid itself holds only machines/splitters/mergers, never IO blocks. Strip = flex row, 1540px wide, one 154px block per column, transparent placeholders for empty columns. Block is 154×90px: direction arrow + ext. lift label, material + rate (Barlow Condensed 13px), source/dest node ref coloured by that node's colour, floor destination (muted, opacity 0.6).

**Strip placement and arrow direction depend on floor type — this is not symmetric, and is the single most commonly-violated rule in the repo. Check it explicitly on every floor:**

| Floor type | Input strip | Output strip | Arrow direction (both strips) |
|---|---|---|---|
| **LB (load-balancer) sub-floor** | above the grid | below the grid | **↓ down**, always |
| **Normal (production) floor** | below the grid | above the grid | **↑ up**, always |

Every direction arrow on a given floor points the same way — input and output rows never mix arrow directions on the same floor. The arrow encodes the *diagram's* reading direction for that floor type (LB floors read top-to-bottom, production floors read bottom-to-top), **not** the literal physical direction material travels between real floors in the build — do not flip an arrow just because a specific belt happens to physically descend from a floor above; the rule is fixed per floor type, not per belt.

## Node colour system (the core traceability mechanism)

- Every splitter/merger gets a unique colour from: `#58C4E8 #F5A623 #B57BEA #5DD67A #4FC3F7 #FFB74D #80CBC4 #AED581 #4DD0E1 #FF8A65 #90CAF9 #F48FB1 #FFF176 #9CCC65 #7986CB`, declared as CSS vars (`--spl1`, `--mrg1`, ...).
- A node's colour is used for: its own centre label, and the colour of *every port label that references it* (on both ends of the connection).
- **To trace a connection: read a port label's colour, find the node whose own label matches that colour.**
- Terminal ports (belt goes straight to a machine or IO strip, no node on the other end) use the **material** colour, not a node colour.
- Splitter: material background + solid material border. Merger: fixed dark teal `#0a1a1a` background + `2px dashed #1a5a5a` border — merger styling never varies by material, so splitters/mergers are visually distinct at a glance.
- Naming: `SPL1`, `SPL2`, `MRG1`... uppercase, no space, numbered from 1. Combined-network floors prefix with the network id (`WMRG1`, `IMRG1`).
- Port zone positions (140×140 block): centre `47,47`; top `47,0`; bottom `47,93`; left `0,47`; right `93,47`.
- **Port placement rule**: splitter input always top port; merger output always bottom port. Remaining ports assigned by the physical direction of the connected node. This is the *post-flip* convention — supersedes any older file that predates it.
- Port label format: node name + rate stacked, e.g. `MRG1 / 15/m`. No arrows, no "in"/"out" text.
- Every LB floor gets a node colour key panel below the diagram (dark bg `#0d1018`, swatch + name per node).

## Machine input tags

Every machine shows coloured pill tags for its per-minute ingredient consumption.

**`tag rate = recipe ingredient quantity × machine output rate at actual clock speed`** — never copy from IO block supply rates; manifold oversupply means belt rate ≠ consumption rate. When a machine feeds another machine on the same floor, use an **internal feed tag** instead of an input-strip entry — internal feeds never appear on the input strip.

## Material colours

| Material | Hex | | Material | Hex |
|---|---|---|---|---|
| Iron ore | `#378ADD` | | Steel ingots | `#D85A30` |
| Iron ingots | `#A8B8C8` | | Concrete | `#7F77DD` |
| Iron rods | `#1D9E75` | | Encased industrial beams | `#BA7517` |
| Iron plates | `#D85A30` | | Steel beams | `#c4880a` |
| Screws | `#888780` | | Steel pipes | `#6abf25` |
| Reinforced iron plates | `#BA7517` | | Foundries (steel ingot producers) | `#993556` |
| Rotors | `#D4537E` | | Copper ore | `#C0622A` |
| Smart plating | `#639922` | | Copper ingots | `#E8924A` |
| Modular frames | `#7F77DD` | | Copper wire | `#D4B84A` |
| Coal | `#888780` | | Cable | `#6abf25` |
| Limestone | `#1D9E75` | | Copper sheet | `#7F77DD` |

Ore/ingot pairs (iron, copper) are intentionally different shades — never reuse ore colour for ingot elements. Cable/steel pipes share green and copper sheet/concrete/modular frames share purple intentionally — disambiguate by label text, not colour.

## Non-negotiable maths rules

- Verify every conservation equation (produced = consumed + surplus, splitter outputs equal, belt tier ≥ trunk rate) **before writing any HTML**. Lock the edge list first.
- 2-way splitter → exact halves; 3-way → exact thirds. An uneven required split (e.g. 30/20 from 50) is not achievable with splitters alone — fix the input rate or design a splitter+merger network. Never fudge this.
- Machine consumption = recipe ingredient qty × machine output rate, never per-item quantities treated as per-minute.

## Verification

Every factory must pass the full floor-by-floor trace test (see `skills/trace-test/SKILL.md`) before being marked complete in `index.html`. Do not guess through ambiguous topology diagrams — flag and resolve collaboratively (node positions in a topology diagram carry no floor-plan layout meaning).

Separately, use `skills/style-audit/SKILL.md` to check existing floors for legacy-style content (pre-flip port orientation, internal IO blocks, old merger styling, etc.) that needs migrating to the current conventions above. Trace-test checks maths; style-audit checks presentation — a floor can fail either independently.

## Skills index

| Skill | Use for |
|---|---|
| `topology-diagram-to-edge-list` | Turning a diagram into a locked edge list — first step of any new floor/factory. |
| `production-maths` | Recipe/clock/splitter/belt-tier calculations, standalone or as part of a build. |
| `new-floor-plan` | Full build sequence for a new factory page, production floor, or LB sub-floor. |
| `new-material-onboarding` | Adding a new material's colour before it appears in any HTML. |
| `trace-test` | Mandatory maths/rate verification before marking a factory complete. |
| `style-audit` | Checking existing floors for legacy-style conventions that need migrating. |
| `geometry-check` | Checking node/machine/IO placements for grid collisions or overflow. |
| `cross-reference-propagation` | Finding every file that needs updating after a change on one floor. |

Companion read-only subagents (`.claude/agents/`) exist for the audit-style skills: `trace-tester`, `style-auditor`, `geometry-checker`, `xref-finder` — prefer these for verification passes so the check isn't relying on the same context that made the edit.

## Workflow

New factory → see `skills/new-floor-plan/SKILL.md` for the build sequence (production chain design → main factory page → floor plan files → trace test). After any edit, run `cross-reference-propagation` before considering it done.
