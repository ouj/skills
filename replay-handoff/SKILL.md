---
name: replay-handoff
description: Analyze an in-progress git branch, compare it with the current master/main using a subagent, derive practical lessons, and generate a concise redo handoff. Use when restarting a messy branch, redoing work cleanly, extracting lessons from current changes, or preparing another agent to verify the handoff, align with the user, and rebuild from the default branch.
---

# Replay Handoff

## Purpose

Create a redo handoff from the current branch. Do not finish the branch. Preserve the intended change, identify what should be done better, and prepare a fresh agent to rebuild cleanly from `master` or `main`.

## Hard Rules

- Do not edit the repository except for creating the handoff document, unless the user explicitly asks.
- Treat the current diff as evidence, not as the correct implementation.
- Use a subagent to check current default-branch code for the changed areas.
- Make the handoff concise, pragmatic, and easy to verify.
- Tell the receiving agent to verify the handoff and align with the user before any code edits.

## Workflow

1. Inspect the branch.
2. Infer intent from the branch and diffs.
3. Check default-branch reality with a subagent.
4. Derive lessons for a cleaner redo.
5. Write the redo handoff.
6. Return the handoff path and a short summary.

## Inspect The Branch

Start with:

```bash
git branch --show-current
git status --short
git diff --stat
git diff --cached --stat
git log --oneline --decorate -n 10
git diff --name-only
```

Then read staged and unstaged diffs. Prioritize files with strong intent signals:

- New, deleted, renamed, staged, or heavily edited files
- TODOs, placeholders, schema changes, migrations, shared contracts, generated files
- Tests, type definitions, API surfaces, UI state, or cross-service boundaries

Read enough surrounding code to understand local patterns, call sites, constraints, and nearby validation.

## Infer Intent

Ground the inferred goal in concrete evidence:

- Branch name and recent commits
- Changed files and public interfaces
- Test names/assertions
- TODOs and deleted/replaced behavior
- Validation failures, if available

If intent is ambiguous, state the ambiguity in the handoff instead of forcing a single story.

## Check Default Branch

Use a subagent for the default-branch check while the main agent continues analyzing the current branch.

Subagent prompt shape:

```text
In this repository, determine the default branch (`origin/main` or `origin/master`) and inspect the latest available default-branch version of these paths: <paths>. Report drift, newer abstractions, renamed APIs, or conflicts that matter for redoing the current branch cleanly. Do not edit files.
```

Prefer a fresh remote-tracking branch when available:

```bash
git remote show origin
git fetch origin
git branch -r --list 'origin/main' 'origin/master'
git show origin/main:<path>
git show origin/master:<path>
```

If fetch is blocked, use the newest local `origin/main` or `origin/master` and mark freshness as unverified. The main agent should also identify the default branch independently and use the subagent result as a cross-check.

## Derive Lessons

Extract only lessons that change the redo:

- What to preserve from the current branch
- What not to port
- Which names, abstractions, or boundaries should change
- Which default-branch APIs or patterns should be used
- Which generated files, migrations, or contracts need care
- Which validation is necessary
- Whether the redo should be split across commits or PRs

Avoid generic advice. Tie each lesson to a file, command, behavior, or contract.

## Create The Handoff

Create and read the handoff file before writing:

```bash
d=$(mktemp -d) && touch "$d/replay-handoff.md" && echo "$d/replay-handoff.md"
```

Use this structure:

```md
# Replay Handoff

## Goal

## Current Branch Evidence

## Inferred Intent

## What To Preserve

## What To Do Differently

## Default Branch Reality Check

## Instructions For The Receiving Agent

## Clean Redo Plan

## Validation Plan

## Open Questions / Risks

## Suggested Skills
```

## Handoff Style

- Prefer short bullets over paragraphs.
- Include only facts that change the redo plan, expose risk, or help verify intent.
- Do not paste long diffs, logs, command output, or full test failures.
- Cite files, branches, commits, commands, PRs, or URLs instead of copying large artifacts.
- Avoid repeating the same fact across sections.
- Keep evidence selective; this is not a full audit trail.

`## Goal` should be one short statement of the desired change. It may name the current branch as the original source. Keep evidence, lessons, and implementation detail out.

```md
## Goal

Redo the assistant attachment commit flow cleanly from `origin/master`, using `ofigma/attachment-send-path` as the original source branch.
```

## Receiving Agent Instructions

In `## Instructions For The Receiving Agent`, require the next agent to:

1. Start from a clean workspace on the verified default branch.
2. Read the handoff, then independently inspect referenced files, branches, commits, diffs, and tests.
3. Report drift, missing context, or incorrect assumptions before proposing changes.
4. Align with the user on design and intent before implementation:
   - Summarize the current system and relevant interfaces.
   - Ask one lettered multiple-choice question at a time when input is needed.
   - Compare 2-3 clean approaches when direction is not obvious.
   - Use before/after diagrams for multi-component changes.
   - Use illustrative code snippets only; do not edit files yet.
5. Present a concise design or redo spec with interface changes, trade-offs, implementation plan, and validation plan.
6. Wait for explicit user approval before any file edits.
7. Ask before adding new tests unless already approved.

## Clean Redo Plan

Keep the plan minimal and ordered:

1. Start from the verified default branch.
2. Recreate the smallest coherent contract or behavior change.
3. Port useful code selectively from the current branch.
4. Prefer current default-branch abstractions over branch-local inventions.
5. Regenerate generated files only after source changes are stable.
6. Run targeted validation before widening scope.
7. Split work when deploy order or review clarity requires it.

Call out what not to port when that is the main lesson.

## Output

Return:

- Handoff file path
- Current branch
- Default branch checked
- Whether default-branch freshness was verified
- Short redo summary
