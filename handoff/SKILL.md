---
name: handoff
description: Compact the current conversation into a handoff document for another agent to pick up.
argument-hint: "What will the next session be used for?"
---

# Handoff

Use this skill to produce a compact handoff document for a fresh agent.

The handoff should preserve decisions, current state, blockers, and the next useful actions without restating large artifacts that already exist elsewhere.

## Workflow

1. Read the current conversation and identify:
- The user's goal
- Work already completed
- Open questions, blockers, and risks
- Concrete next steps
- Existing artifacts that already capture detail, such as PRDs, plans, ADRs, issues, commits, diffs, or generated files

2. If the user supplied arguments, treat them as the expected focus of the next session.
- Tailor the handoff toward that next task.
- Emphasize the most relevant state and deprioritize unrelated context.

3. Create the output path with:

```bash
mktemp -t handoff-XXXXXX.md
```

4. Read the newly created file before writing to it.
- This is required even if the file is empty.

5. Write a concise Markdown handoff document to that path.

## Writing Rules

- Keep it compact and high signal.
- Do not duplicate content already captured in another artifact.
- Reference existing artifacts by path or URL instead of copying their contents.
- Prefer concrete facts over narrative recap.
- Call out unresolved assumptions explicitly.
- Include suggested skills for the next session when they would help.

## Suggested Structure

Use this structure unless the task clearly needs a variation:

```md
# Handoff

## Goal

## Current State

## Artifacts
- `/abs/path/to/file`
- `https://...`

## Decisions

## Open Questions / Risks

## Next Steps

## Suggested Skills
- `skill-name`: why it is relevant
```

## Output Expectations

- Return the handoff path in the chat response.
- Briefly summarize what the handoff covers.
- Mention the suggested skills explicitly, or say that no additional skills are recommended.
