---
name: trace-test
description: Run the mandatory floor-by-floor verification pass on Satisfactory factory documentation before it can be marked complete. Use whenever a factory or floor has just been built or edited, when the user asks to "check", "verify", "audit", or "trace" a factory's floors, or before adding/updating a factory's status to complete in index.html. This is a required gate, not optional QA — do not skip it after building floors.
---

# Floor-by-Floor Trace Test

A full, mechanical verification pass across every floor of a factory, run **after** all floor plan files exist and **before** the factory (or the edited floors) is considered done. This should be run with fresh eyes on each floor rather than trusted from memory of having just built it — if a subagent is available, prefer delegating this to one so the check isn't relying on the same context/assumptions that did the build.

## Per-floor checks

For each floor, in order from Ground upward:

1. **Input strip** — material, rate, source label, and source-node colour reference are all correct.
2. **Machines** — input consumption matches what's supplied; output rate = recipe × actual clock speed; input tags present and numerically correct (`recipe ingredient qty × machine output rate`, never a belt supply rate).
3. **Output strip** — material, rate, destination label correct.
4. **Handoff** — this floor's output strip matches the next floor's input strip: same materials, same rates, matching node-colour references across the boundary.
5. **Floor maths** — total inputs consumed = total produced by this floor's machines; no unexplained surplus or deficit.

## Cross-floor checks (whole factory)

- Every LB output strip matches what the next production floor expects on input.
- All rates match at every handoff point, exactly.
- Material labels are correct at every stage (watch shared colours: cable/steel pipes, copper sheet/concrete/modular frames — confirm via label text, not colour).
- Store-bound outputs are correctly absent from the next floor's input strip.
- Bypass belts (materials skipping a floor) are sourced from the correct originating floor.
- Internal (same-floor) consumption appears only as internal feed tags, never as input-strip entries.

## Node network checks (every LB sub-floor)

- Every splitter's outputs are exactly equal (÷2 for 2-way, ÷3 for 3-way — no exceptions).
- Every belt is traced end-to-end through the splitter/merger network.
- Every merger's output = sum of its inputs.
- Sum of all output-strip rates = sum of all input-strip rates for that floor (nothing created or destroyed).
- Node colour consistency: output port labels use the destination node's colour, input port labels use the source node's colour, terminal ports use material colour. Pick any port label's colour and confirm it matches exactly one node's own label colour — if it doesn't, that's a defect.

## On failure

Do not patch around a failed check in isolation if it's systematic (e.g. every port on a floor uses the pre-flip orientation, or a naming convention is violated across several nodes) — fix it globally across all affected nodes/files per `CLAUDE.md`, then re-run the full test. Flag anything genuinely ambiguous (e.g. a splitter vs. merger shape that can't be told apart in a source diagram) rather than guessing — use the behavioral arithmetic (flow conservation) as ground truth for resolving it.

## Output

Report, per factory: pass/fail per floor, and an itemized list of any failed checks with the specific floor, node, or handoff involved. A factory is only ready to be marked complete in `index.html` once every check above passes with no open items.
