---
name: production-maths
description: Work out or verify Satisfactory production chain maths — machine consumption rates, clock speed scaling, splitter/merger validity, belt tier requirements, manifold-vs-load-balancer decisions — independent of building any floor HTML. Use when the user asks "what if I clock this at X%", "can I split N into A and B", "what belt do I need for this rate", or wants to explore production options before committing to a design. Also used as the maths foundation inside skills/new-floor-plan's Step 1.
---

# Production Maths

Pure calculation/verification — no HTML, no file edits. Useful standalone for exploring options, and used by `new-floor-plan`'s Step 1 when locking a design.

## Machine consumption

**`ingredient consumption per minute = recipe ingredient quantity × machine output rate at actual clock speed`**

Never use the recipe's per-item ingredient quantity as if it were already a per-minute rate — it's per unit of output, and must be scaled by the machine's actual output rate.

Example: Rotor assembler at 100% produces 4/min; recipe is 5 rods + 25 screws per rotor → consumption is `4 × 5 = 20 rods/min` and `4 × 25 = 100 screws/min`.

## Clock speed scaling

- Output rate and ingredient consumption both scale **linearly** with clock speed: a machine at 150% clock produces 1.5× base output and consumes 1.5× base ingredients.
- Power draw scales **super-linearly** (roughly with the square of clock %, per Satisfactory's actual mechanics) — worth flagging if a proposed overclock has a large power implication, even though this project's instructions don't track power budgets explicitly; mention it rather than silently ignoring it.
- Overclocking requires power shards (1 shard per 50% above 100%, up to 250% with 4 shards) — per player preference, overclocking is preferred over duplicating machines where the clock increase is achievable.

## Splitter validity

- A 2-way splitter always produces **exact halves** of its input. A 3-way always produces **exact thirds**.
- If a required split is uneven (e.g. 30/20 from a 50/min input), it is **not achievable with splitters alone**. Resolve by either:
  1. Adjusting the input rate so the required split becomes even (e.g. change upstream production so the split target is a clean fraction), or
  2. Designing an explicit splitter+merger network that recombines flows to reach the uneven target.
- Never approximate an uneven split as "close enough" — verify the exact numbers before committing to a design.

## Belt tier requirements

| Tier | Cap (items/min) |
|---|---|
| Mk.1 | 60 |
| Mk.2 | 120 |
| Mk.3 | 270 |
| Mk.4 | 480 |

Check every trunk belt's actual rate against these caps. If a trunk is at or near a cap, that's a candidate for a belt warning box on the relevant floor (see `CLAUDE.md`).

## Manifold vs. load balancer

Per player preference, **manifolds are preferred over load balancers** for space efficiency. Before proposing a splitter/merger network:

1. Check whether a manifold (shared trunk belt feeding multiple machines directly, each machine pulling what it needs, belt running at or near cap) can satisfy the requirement.
2. Only fall back to an explicit LB network when a manifold is genuinely unworkable — e.g. the split must be exact and uneven, or machines have meaningfully different demand that a manifold's implicit distribution can't guarantee.
3. State explicitly which case applies when proposing a design, so the choice isn't ambiguous later.

## Conservation check

Before finalizing any design: **produced = consumed + surplus**, at every node and at the floor/factory level, including any recirculation loops. This must close exactly — an unexplained surplus or deficit means an error somewhere in the maths above, not an acceptable rounding gap.

## Output format for a maths pass

When asked to verify or design production maths, present:
1. The locked node-by-node edge list (source → destination, material, rate).
2. Per-machine consumption calculations showing the formula applied.
3. Splitter/merger validity check for every split/merge point.
4. Belt tier check for every trunk.
5. Final conservation check (totals in = totals out).

Only once all five close cleanly is the design ready to hand to `new-floor-plan` for HTML implementation.
