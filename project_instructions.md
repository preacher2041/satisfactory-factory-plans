Satisfactory Factory Documentation — Project Instructions

Game context

Satisfactory 1.0, Phase 3 in progress
Phases 1 and 2 complete
Tiers 0–6 unlocked (Oil Processing, Jetpack, Monorail Train Technology)

Completed Phase 3 requirements


Encased Industrial Beams — factory built and running ✅
Modular Frames — in progress
Rubber (200) — not started, oil processing not yet set up


Production already running


Copper Sheets, Copper Wire, Cables
Concrete: 30/min
Iron Plates, Screws, Iron Rods: 60/min
Rotors, Steel Beams, Steel Pipes
Reinforced Iron Plates: 5/min (2.5/min to Smart Plating, 2.5/min available)
Smart Plating
Encased Industrial Beams: 30/min



Player preferences


Prefers manifolds over load balancers for space efficiency — always recommend manifold-compatible belt speeds (trunk belt at or near cap) rather than load balancer solutions unless manifold is genuinely unworkable
Overclocks miners rather than building multiple miners where possible
Power shards available for overclocking
Uses Miner Mk.2
Builds multi-storey factories with consistent floor heights
Prefers 8m × 8m foundations, 12m floor height (3 wall stacks), uniform across all floors
Belt lifts run on exterior of building
Storage on ground floor for easy access



Factory HTML documents

Core reference files
The following HTML files exist as project files and serve as templates for all future factory documentation:
index.html — the master factory index page. Lists all factories with their status (complete / in progress / planned), output rates, footprint and machine counts. Links to each individual factory page. When a new factory is designed and built, a new card must be added to this index with the factory name, status, outputs and key stats, and a link to its HTML file.
iron_megafactory.html — the Iron Megafactory main page. Legacy template for the main factory page structure. Reference for summary bar, floor plan grid, and production chain breakdown layout.
copper_factory.html — the Copper Factory main page. This is the canonical template for all future multi-floor factory main pages. It implements the current design language described in these instructions.
copper_floor1.html / copper_floor2.html / copper_floor3.html — canonical templates for production floor plan files. External IO strips, machines-only grid.
copper_lb0b.html / copper_lb2b.html — canonical templates for LB sub-floor plan files. External IO strips, node colour system, SPL/MRG naming, 140×140px blocks.
steel_eib_factory.html — legacy template for simpler single-diagram factory pages (factories that do not require floor plans).
The full set of project files covers three documented factories:

Iron Megafactory — floor0_ground.html through floor5_mf.html, lb0b_ore_split.html through lb4b_rip_rods.html
Steel & EIB Factory — eib_ground.html through eib_floor4.html, eib_lb0b.html through eib_lb3b.html
Copper Factory — copper_ground.html through copper_floor3.html, copper_lb0b.html through copper_lb3b.html

Before starting any new floor or factory, search the project files to check whether a file already exists and to find the most relevant existing floor as a reference. Do not rely solely on the canonical templates listed above — a recently built floor of the same type may be a closer match.


When building a new factory

Step 1 — Design the production chain

Reading topology diagrams: Topology diagrams with clean tree structures and no crossing edges can usually be parsed reliably. Diagrams with lateral flows, crossing edges, or ambiguous node types may be difficult to parse accurately from the image alone. In these cases, do not guess — describe what can be extracted confidently and work through the ambiguous parts collaboratively before writing any code. Node positions in a topology diagram have no bearing on floor plan layout.

Work out machine counts, clock speeds, belt rates and routing before touching any HTML. Verify the full production maths:

Every input rate must equal demand at that machine
Total material budgets must balance (produced = consumed + surplus)
Belt tiers must be sufficient for every trunk rate
Unequal splits (e.g. 30/20 from 50/min) cannot be achieved with splitters alone — check whether the input can be adjusted to give a clean split, or use a splitter+merger network. Always verify the maths before committing to a design.
Splitter outputs must always be equal: a 2-way splitter always produces two equal halves, a 3-way always produces three equal thirds. If the required split is not achievable with equal outputs (e.g. 3/5 and 2/5 of a total), the input rate must be changed or a splitter+merger network must be designed. Verify every splitter in the design produces mathematically valid equal outputs before building the floor plan.
For every machine, calculate the per-minute input consumption by multiplying each recipe ingredient quantity by the machine's output rate (accounting for clock speed). Example: a Rotor assembler at 100% produces 4/min; recipe is 5 rods + 25 screws per rotor; therefore consumption is 20 rods/min + 100 screws/min. Never use per-item recipe quantities as if they were per-minute rates.



Step 2 — Build the main factory page

Follow the structure of copper_factory.html. The page is a single full-width column with four sections in order:

1. Header

Factory name as <h1>, sub-title line, back link to index.html.

2. Build summary bar

A full-width horizontal grid directly below the header. Use:

cssdisplay: grid;
grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));

Each cell has a border-right and border-bottom so the grid reads as a connected panel. No sticky positioning. Five standard sections, each in its own cell:

SectionContents// FactoryFootprint, total height, foundations, machine count, power shards// Floor heightsEvery floor and LB sub-floor with its height in metres// Belt requirementsMaterial, belt tier, and trunk rate for every constrained belt// Clock speedsEvery non-100% machine with clock speed and per-machine output rate// Buildings requiredCount of each machine type

Use the existing .summary-section-title, .summary-row, .summary-key, .summary-val, and .belt-chip CSS classes for consistent styling.

3. Floor plans grid

Immediately below the summary bar. A repeat(auto-fill, minmax(160px, 1fr)) card grid linking to all floor plan files. Production floors use var(--surface2) background; LB sub-floors use a darker #0d1018 background. Each card has a colour-coded left border matching its primary material and shows the floor label, height, and name.

4. Production chain breakdown

Full-width section below the floor plans. Covers recipes, throughput calculations, miner clock speed reasoning, load balancer/manifold notes, and factory outputs. Use .chain-section, .chain-header, .chain-grid, .chain-card classes following the pattern in copper_factory.html.

General styling rules


Same dark industrial theme, fonts (Barlow Condensed + Barlow + Share Tech Mono) and CSS variable colour system as copper_factory.html
Single-column full-width layout — no side panels, no sticky elements
Add new material colour variables to :root CSS if new materials are introduced
Once the factory page is created, add a card to index.html



Step 3 — Build floor plan files

For every multi-storey factory, generate a full set of floor plan HTML files. Reference the copper factory floor plan files as the canonical template system.

Grid system

Floor plans are sized to fit a 1665 × 1000px viewport with the grid filling the available width after accounting for the row label column and page padding.


FS = 154px per foundation (8m × 8m)
Grid: 10 columns × 6 rows = 1540 × 924px
Page padding: 20px top/bottom, 24px sides
Row label column: 52px wide
Total page width used: 52 + 1540 + 24×2 = 1640px — fits within 1665px viewport ✓


Block sizes (at FS=154):


Splitter/merger block size: 140×140px (centred in 154px cell, 7px offset each side)
Port zone size: 47×47px
Machine block sizes unchanged (see table below)


Machine sizes (at FS=154px)

MachineReal sizePixel sizeSpanSmelter6m × 9m116 × 232px1W × 2DConstructor8m × 10m128 × 232px1W × 2DAssembler10m × 15m256 × 256px2W × 2DStorage container5m × 10m96 × 232px1W × 2D

Font sizes

Diagram label fonts (inside .ml layer only)

ElementFontSizeSplitter/merger node name (centre zone)Share Tech Mono13pxSplitter/merger port labels (zone text)Share Tech Mono11pxIO block material nameBarlow Condensed13pxIO block rateShare Tech Mono13pxIO block source/destinationShare Tech Mono10pxCell labelsShare Tech Mono13px

Machine node fonts

ElementFontSizeMachine name (.m-lbl)Barlow Condensed15pxMachine sub-label (.m-sub)Share Tech Mono12pxInput tagsShare Tech Mono10px

Page chrome fonts (outside .ml layer)

ElementFontSizePage heading h1Barlow Condensed24pxSub-titleShare Tech Mono11pxNav barShare Tech Mono11pxRow/column axis labelsShare Tech Mono11pxLegendShare Tech Mono11pxNotesShare Tech Mono12pxBelt warning boxShare Tech Mono13px


External IO strips — the standard IO system

All inputs and outputs on every floor (both production floors and LB sub-floors) use external IO strips — horizontal rows of individual blocks that sit outside the grid, above and below it. The grid itself contains only machines, splitters, and mergers. No IO blocks appear inside the grid.

Input strip — positioned above the grid, between the column labels and the grid border.
Output strip — positioned below the grid, immediately after the grid border.

Each strip is a flex row 1540px wide, with one 154px block per column (10 blocks total). Empty columns use transparent placeholder blocks to maintain alignment.

Individual IO block (154px wide × 90px tall):

html<div class="io-block" style="background:[material-bg];border:1px solid [material-bd];">
  <span ...>↑ ext. lift</span>          <!-- or ↓ ext. lift -->
  <span ...>Material 30/min</span>       <!-- material name + rate, Barlow Condensed 13px -->
  <span ...color:[source-node-colour]>← SPL4</span>   <!-- source/dest node, coloured by that node -->
  <span ...opacity:0.6>→ Fl.1 Sm1</span>  <!-- floor destination, muted -->
</div>

Source/destination colouring rule: The node reference label on an IO block (e.g. ← SPL4, → MRG1) is coloured using that node's unique colour — matching the node label colour on the LB floor where that node lives. This extends the node colour system across floor boundaries so the chain is visually traceable.

CSS class:

css.io-strip { display: flex; gap: 0; width: 1540px; }
.io-block  { width: 154px; height: 90px; display: flex; flex-direction: column;
             align-items: center; justify-content: center; gap: 1px;
             border-radius: 5px; flex-shrink: 0; }


Pass-through LB floors

When a LB floor performs no splitting or merging — only routing belts from one side of the building to the other — it is documented as a pass-through floor. The grid is completely empty. Only the IO strips are shown, with input and output blocks aligned to the columns they enter and exit. A note explains the routing purpose.


Combined-network LB floors

A single LB floor may host two or more completely independent belt networks of different materials. Each network occupies its own columns. A vertical divider line is drawn on the grid at the column boundary between networks. Each network uses its own node colour palette; the node colour key panel lists both networks separately.


Splitter and merger nodes

All splitter and merger nodes use 140×140px blocks, centred within their 154px grid cell (7px offset each side). Port zones are 47×47px, positioned at the top/bottom/left/right of the block.

Node naming: SPL1, SPL2, MRG1, MRG2 etc. — always uppercase prefix + number, no space, numbered from 1. On combined-network floors, prefix the network identifier to avoid ambiguity: WMRG1 (wire merger 1), IMRG1 (ingot merger 1), SPL1 etc.

Visual distinction between splitters and mergers:

TypeBackgroundBorderSplitterMaterial background colour (e.g. --copper-ore-bg)Solid material border colourMergerDark teal #0a1a1a2px dashed teal #1a5a5a

The dark teal merger background is fixed regardless of material — it provides immediate visual distinction from splitters at a glance, reinforced by the dashed border.

.pz CSS helper class:

css.pz {
  position: absolute;
  width: 47px; height: 47px;
  display: flex; flex-direction: column;
  align-items: center; justify-content: center;
  font-family: 'Share Tech Mono', monospace;
  line-height: 1.2; text-align: center;
}

Port zone positions within a 140×140px block:


Centre zone: left: 47px; top: 47px
Top zone: left: 47px; top: 0px
Bottom zone: left: 47px; top: 93px
Left zone: left: 0px; top: 47px
Right zone: left: 93px; top: 47px

Grid placement

Nodes should be placed so that splitters sit in the upper rows of the grid (closer to the input strip) and mergers in the lower rows (closer to the output strip), reflecting the top-to-bottom flow direction. Leave row A and row F as breathing room where topology permits.

Port placement rules

Splitter input always on the bottom port
Merger output always on the top port
Remaining ports (left, right, top for splitters / left, right, bottom for mergers) are assigned based on the physical direction of the connected node relative to the current node

Port label format

Node name and rate stacked in the port zone — e.g. MRG1 / 15/m. No arrow characters, no directional text such as "in" or "out". This applies to all ports including terminal ports, which use the material colour instead of a node colour.

Node colour system

Every splitter and merger node is assigned a unique colour. This colour is used for:


The node's own label in the centre zone
Output port labels on this node — the label text is coloured using the destination node's colour
Input port labels on this node — the label text is coloured using the source node's colour


This means: to trace a connection, find a port label on one node, note its colour, and look for the node whose label matches that colour — that is the connected node.

Terminal outputs (belts going directly to machines or to IO strips, with no splitter/merger on the other end) use the material colour, not a node colour.

Terminal inputs (belts arriving directly from machines or IO strips) also use the material colour.

Node colour palette — assign unique colours from this list, one per node per floor:
#58C4E8 #F5A623 #B57BEA #5DD67A #4FC3F7 #FFB74D #80CBC4 #AED581 #4DD0E1 #FF8A65 #90CAF9 #F48FB1 #FFF176 #9CCC65 #7986CB

Declare node colours as CSS variables in :root:

css--spl1: #58C4E8;
--mrg1: #4ecfcf;
--spl2: #B57BEA;
/* etc. */

Node colour key panel — below every LB floor diagram, include a dark-background panel listing every node with its colour swatch:

html<div style="...background:#0d1018;border:1px solid #2a3048;...">
  <span style="color:#d4d8e8;">// Node colours</span><br>
  <span style="color:var(--spl1)">■ SPL1</span> &nbsp;
  <span style="color:var(--mrg1)">■ MRG1</span> &nbsp;
  ...
</div>


Machine input tags

Every machine node must include a row of colour-coded input tags showing its per-minute ingredient consumption. One tag per ingredient, displayed as coloured pills matching the material's colour palette.

Tag rate formula:


tag rate = recipe ingredient quantity × machine output rate (at actual clock speed)



Never copy rates from IO blocks or belt supply figures — IO blocks show what is supplied, tags show what the machine consumes. These are often different values when manifold oversupply is in use.

Tag HTML pattern:

html<div style="display:flex;gap:4px;margin-top:4px;flex-wrap:wrap;justify-content:center;">
  <span style="font-family:'Share Tech Mono',monospace;font-size:10px;
    color:[FG];background:[BG];border:1px solid [BD];
    border-radius:3px;padding:1px 5px;">← Material X/m</span>
</div>


Internal feed tags (on-floor consumption)

When a machine consumes output from another machine on the same floor (rather than from a belt arriving via the input strip), add small coloured tags inside the machine node showing the internal feed. These materials should NOT appear in the input strip.


Navigation bar

Every floor file has a nav bar at the top with:


Prev/next buttons
All floor quick-jump links (current floor highlighted)
Factory link on the right


The last floor shows — top floor — in place of a next button. Navigation order follows the floor sequence from Ground upward.


Belt speed warnings

Add an amber warning box at the bottom of any floor where a belt trunk is at or near a belt tier cap. Include: which belt is constrained, what tier is required, and what happens if a slower belt is used.


LB sub-floor structure

Every LB sub-floor follows this structure:


Nav bar at top
Page heading (h1) and sub-title
Column labels
Input strip (above grid) — individual blocks per input belt, coloured by destination node
Grid — splitter/merger nodes only, no IO blocks inside
Output strip (below grid) — individual blocks per output belt, coloured by source node
Legend — splitter and merger swatches
Node colour key panel
Notes — explaining any non-obvious routing
Belt speed warning (if applicable)



Step 4 — Run the floor-by-floor trace test

This step is mandatory before finalising any factory. After building all floor plan files, run a full automated trace checking every floor in sequence.

For each floor, verify:


Input strip — material, rate, source label and source-node colour are correct
Machines — input consumption matches supply; output rate matches recipe × clock speed; input tags present and correct
Output strip — material, rate, destination label are correct
Handoff — output strip of this floor matches input strip of the next floor (same materials, same rates, matching node colour references)
Maths — total inputs consumed = total produced by upstream machines; no unexplained surplus or deficit


Cross-floor checks to always run:


Every LB output strip matches what the next production floor expects in its input strip
All rates at every handoff point are equal
Material labels are correct at each stage
Store-bound outputs are correctly absent from the next floor's inputs
Bypass belts (materials that skip a floor) are correctly sourced from the right floor
Internal consumption is shown via internal feed tags, not in the input strip
Machine input tags show the correct consumption rate (recipe ingredient quantity × machine output rate) — not the belt supply rate


Node network checks (for every LB sub-floor):


Every splitter output must be equal — a 2-way split gives input ÷ 2, a 3-way gives input ÷ 3
Trace every belt end-to-end through the splitter/merger network
Merger outputs must equal the sum of their inputs
Total of all output strip rates must equal total of all input strip rates — no material created or destroyed
Node colour consistency — output port labels use the destination node's colour; input port labels use the source node's colour; terminal ports use material colour


If any check fails, fix before proceeding. Do not finalise the factory until all checks pass.


Colour coding by material (consistent across all factory pages)

MaterialColourHexIron oreSaturated blue#378ADDIron ingotsSteel grey-blue#A8B8C8Iron rodsTeal#1D9E75Iron platesCoral/orange#D85A30ScrewsGrey#888780Reinforced iron platesAmber#BA7517RotorsPink/rose#D4537ESmart platingGreen#639922Modular framesPurple#7F77DDCoalGrey#888780LimestoneTeal#1D9E75Steel ingotsCoral#D85A30ConcretePurple#7F77DDEncased industrial beamsAmber#BA7517Steel beamsAmber/orange#c4880aSteel pipesGreen#6abf25Steel ingot producers (foundries)Pink#993556Copper oreBurnt orange#C0622ACopper ingotsLight orange#E8924ACopper wireGold/amber#D4B84ACableGreen#6abf25Copper sheetPurple#7F77DD

Note: Iron ore (#378ADD) and iron ingots (#A8B8C8) are intentionally distinct — ore is saturated blue, ingots are desaturated steel grey-blue. Do not reuse ore blue for ingot IO blocks, machine tags, or LB diagrams. Similarly, copper ore (#C0622A) and copper ingots (#E8924A) are distinct shades of orange.

Note: Cable shares green (#6abf25) with steel pipes, and copper sheet shares purple (#7F77DD) with concrete and modular frames. This is intentional — use context (material name labels) to distinguish them.


Load balancer sub-floor diagrams (main factory page only)

The main factory page's production chain breakdown uses the HTML div pattern for LB diagrams (not the floor plan grid system). Follow the established pattern from copper_factory.html:


.chain-card div with dark background
Each network described in prose with a .chain-note for routing logic
Never use SVG for these diagrams
The node colour system and SPL/MRG naming used in the floor plan files should be referenced consistently in prose descriptions