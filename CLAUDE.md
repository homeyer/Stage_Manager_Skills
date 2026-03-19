# Stage Manager — Artful Making Skill Library

A collection of 13 skills for vibe coders who use agentic coding tools (Claude Code, Cursor, Replit). Skills are organized across the Shape-to-Stage flow cycle. The mirror never goes dark.

## Available Slash Commands

### Shape (analyze before building)
- `/shape-find-holes` — Find specification gaps where coding tools will invent behavior
- `/shape-collapsed-options` — Find decisions made before the builder knew they were making them
- `/shape-risk-sequence` — Surface load-bearing assumptions and sequence by cost-if-wrong
- `/shape-soul-check` — Deep read on whether the original animating idea is still alive

### Transition
- `/sense-shape-to-stage-gate` — Five readiness questions before crossing from shaping to staging

### Stage (before, during, and after the build)
- `/stage-chunking` — Break work into flow-cycle-sized pieces for clean prompting
- `/stage-wsjf` — Sequence stories by weighted cost of delay
- `/stage-prompt-craft` — Turn shaped chunks into scoped, guardrailed prompts
- `/stage-prompt-guard` — Annotate prompts to flag the prompt's own invisible decisions before coding
- `/stage-live-mirror` — Compare plan vs. code output per-session, surface what the tool invented
- `/stage-output-review` — Review coding tool output against definition of done and intent
- `/stage-decision-capture` — Full decisions-made manifest after a build, feeds forward into learning

### Any Node
- `/coherence-check` — Lightweight 2-minute gate at any transition point

## Compound Engineering Integration

Modified CE commands that integrate Stage Manager at every phase live in `plugins/compound-engineering/commands/ce/`. These are a fork of the standard CE pipeline with shaping gates, live mirror checkpoints, and decision capture built in.

## Shared References

Reference files used by skills live in `plugins/shared/references/`:
- `invention-zones.md` — 12 architectural zones where coding tools invent without asking
- `tool-selection-zones.md` — Tool choices, alternatives, lock-in risks
- `breakthrough-dimensions.md` — Dimensions builders push on and where options collapse

## Soul Document

`stage-manager-soul.md` contains the animating philosophy: *How you move through your work is what you build.*
