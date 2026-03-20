---
name: ce:brainstorm
description: Explore requirements and approaches through collaborative dialogue before planning implementation
argument-hint: "[feature idea or problem to explore]"
---

# Brainstorm a Feature or Improvement

**Note: The current year is 2026.** Use this when dating brainstorm documents.

Brainstorming helps answer **WHAT** to build through collaborative dialogue. It precedes `/ce:plan`, which answers **HOW** to build it.

**Process knowledge:** Load the `brainstorming` skill for detailed question techniques, approach exploration patterns, and YAGNI principles.

## Feature Description

<feature_description> #$ARGUMENTS </feature_description>

**If the feature description above is empty, ask the user:** "What would you like to explore? Please describe the feature, problem, or improvement you're thinking about."

Do not proceed until you have a feature description from the user.

## Execution Flow

### Phase 0: Assess Requirements Clarity

Evaluate whether brainstorming is needed based on the feature description.

**Clear requirements indicators:**
- Specific acceptance criteria provided
- Referenced existing patterns to follow
- Described exact expected behavior
- Constrained, well-defined scope

**If requirements are already clear:**
Use **AskUserQuestion tool** to suggest: "Your requirements seem detailed enough to proceed directly to planning. Should I run `/ce:plan` instead, or would you like to explore the idea further?"

### Phase 1: Understand the Idea

#### 1.1 Repository Research (Lightweight)

Run a quick repo scan to understand existing patterns:

- Task repo-research-analyst("Understand existing patterns related to: <feature_description>")

Focus on: similar features, established patterns, CLAUDE.md guidance.

#### 1.2 Collaborative Dialogue

Use the **AskUserQuestion tool** to ask questions **one at a time**.

**Guidelines (see `brainstorming` skill for detailed techniques):**
- Prefer multiple choice when natural options exist
- Start broad (purpose, users) then narrow (constraints, edge cases)
- Validate assumptions explicitly
- Ask about success criteria

**Exit condition:** Continue until the idea is clear OR user says "proceed"

### Phase 2: Explore Approaches

Propose **2-3 concrete approaches** based on research and conversation.

For each approach, provide:
- Brief description (2-3 sentences)
- Pros and cons
- When it's best suited

Lead with your recommendation and explain why. Apply YAGNI—prefer simpler solutions.

Use **AskUserQuestion tool** to ask which approach the user prefers.

### Phase 3: Capture the Design

Write a brainstorm document to `docs/brainstorms/YYYY-MM-DD-<topic>-brainstorm.md`.

**Document structure:** See the `brainstorming` skill for the template format. Key sections: What We're Building, Why This Approach, Key Decisions, Open Questions.

Ensure `docs/brainstorms/` directory exists before writing.

**IMPORTANT:** Before proceeding to Phase 4, check if there are any Open Questions listed in the brainstorm document. If there are open questions, YOU MUST ask the user about each one using AskUserQuestion before offering to proceed to planning. Move resolved questions to a "Resolved Questions" section.

### Phase 3.5: Stage Manager Shaping (NEW)

After the brainstorm document is captured, offer Stage Manager shaping to validate the design decisions.

Use **AskUserQuestion tool**:

**Question:** "Before we move to planning — want to run the Stage Manager shaping skills on this brainstorm? They'll surface decisions you've already made without knowing it."

**Options:**
1. **Find the Holes** — Surface specification gaps where coding tools will invent behavior
2. **Collapsed Options** — Find decisions you've committed to without knowing it
3. **Risk Sequence** — Surface load-bearing assumptions and sequence by cost-if-wrong
4. **Soul Check** — Check whether the original animating idea is still alive
5. **Run all shaping skills** — Full shaping pass (takes 5-8 minutes)
6. **Skip shaping** — Proceed directly to handoff

**If user selects a shaping skill:**
- Run the selected skill(s) on the brainstorm document
- After each skill completes, return here and offer to run additional shaping skills or proceed
- Any decisions made during shaping should be added to the brainstorm document under a "## Shaping Decisions" section

**If user selects "Run all shaping skills":**
- Run in sequence: Find the Holes → Collapsed Options → Risk Sequence → Soul Check
- After all complete, summarize key findings and update the brainstorm document

**If user selects "Skip shaping":**
- Proceed to Phase 4

### Phase 4: Handoff

Use **AskUserQuestion tool** to present next steps:

**Question:** "Brainstorm captured. What would you like to do next?"

**Options:**
1. **Review and refine** - Improve the document through structured self-review
2. **Run Stage Manager shaping** - Surface invisible decisions before planning (if not done in Phase 3.5)
3. **Proceed to planning** - Run `/ce:plan` (will auto-detect this brainstorm)
4. **Share to Proof** - Upload to Proof for collaborative review and sharing
5. **Ask more questions** - I have more questions to clarify before moving on
6. **Done for now** - Return later

**If user selects "Share to Proof":**

```bash
CONTENT=$(cat docs/brainstorms/YYYY-MM-DD-<topic>-brainstorm.md)
TITLE="Brainstorm: <topic title>"
RESPONSE=$(curl -s -X POST https://www.proofeditor.ai/share/markdown \
  -H "Content-Type: application/json" \
  -d "$(jq -n --arg title "$TITLE" --arg markdown "$CONTENT" --arg by "ai:compound" '{title: $title, markdown: $markdown, by: $by}')")
PROOF_URL=$(echo "$RESPONSE" | jq -r '.tokenUrl')
```

Display the URL prominently: `View & collaborate in Proof: <PROOF_URL>`

If the curl fails, skip silently. Then return to the Phase 4 options.

**If user selects "Ask more questions":** YOU (Claude) return to Phase 1.2 (Collaborative Dialogue) and continue asking the USER questions one at a time to further refine the design. The user wants YOU to probe deeper - ask about edge cases, constraints, preferences, or areas not yet explored. Continue until the user is satisfied, then return to Phase 4.

**If user selects "Review and refine":**

Load the `document-review` skill and apply it to the brainstorm document.

When document-review returns "Review complete", present next steps:

1. **Move to planning** - Continue to `/ce:plan` with this document
2. **Done for now** - Brainstorming complete. To start planning later: `/ce:plan [document-path]`

## Output Summary

When complete, display:

```
Brainstorm complete!

Document: docs/brainstorms/YYYY-MM-DD-<topic>-brainstorm.md

Key decisions:
- [Decision 1]
- [Decision 2]

Shaping findings: [X holes found, Y collapsed options surfaced] (if shaping was run)

Next: Run `/ce:plan` when ready to implement.
```

## Important Guidelines

- **Stay focused on WHAT, not HOW** - Implementation details belong in the plan
- **Ask one question at a time** - Don't overwhelm
- **Apply YAGNI** - Prefer simpler approaches
- **Keep outputs concise** - 200-300 words per section max
- **Stage Manager shaping is optional but recommended** - The earlier you catch invisible decisions, the less they cost

NEVER CODE! Just explore and document decisions.
