---
name: pr-stack
description: Manage stacked pull requests on top of plain git and GitHub CLI without Graphite. Use when the user wants to create, inspect, restack, move, submit, or sync a stack of dependent branches and PRs while tracking parent branches explicitly and avoiding unsafe history rewrites.
---

# PR Stack

Use this skill for Graphite-style stacked PR workflows built on top of plain `git` and `gh`.

## Mental Model

A stack has three parts:

- A trunk branch, usually the repository default branch.
- A chain or tree of feature branches where each branch has one intended parent.
- A matching set of pull requests whose base branch is that parent, not always trunk.

Prefer explicit local metadata over guesswork:

- `stack.trunk=<branch>`
- `branch.<name>.stackParent=<parent-branch>`

Read those values first. If they are missing, infer cautiously from existing PR base branches or Git ancestry and state that the result is inferred.

## Workflow

1. Resolve stack state before mutating anything.
- Inspect `git status -sb`, the current branch, the repo default branch, and a concise branch graph.
- For PR actions, confirm that `gh` is available and authenticated.
- Never operate on a detached HEAD.

2. Protect history.
- Never rewrite the default branch.
- Never use plain `--force`; prefer `--force-with-lease`.
- Do not create duplicate PRs; check for an existing PR for each branch first.
- If the working tree is dirty, decide whether rebasing or branch switching is safe before continuing.

3. Treat these as the supported core operations.
- `track`: record or repair `stack.trunk` and `branch.<name>.stackParent`.
- `log`: show stack order, parent relationships, and PR status.
- `create`: create a child branch from the current branch, commit intended changes, and record the current branch as the new branch's parent.
- `restack`: rebase descendants in parent-to-child order so each branch contains its parent's tip in history.
- `move`: change a branch's parent, then restack that branch and its descendants.
- `submit`: push changed branches and create or update one PR per branch with base = parent branch.
- `sync`: fetch remote trunk, rebase the bottom branch on trunk, then restack upward.

4. Stop when ancestry is ambiguous and the next step would rewrite history or change PR bases.
- If parentage is unclear, surface the competing interpretations and ask before rebasing, moving, or retargeting PRs.
- If the configured parent conflicts with actual Git ancestry, call that out and reconcile intentionally.

## Graphite Mapping

Distill the Graphite mental model rather than the full CLI:

- `gt create` -> branch from the current branch, commit, then record parent metadata.
- `gt restack` -> ordered rebases of descendants onto their recorded parents.
- `gt move` -> update parent metadata, then restack.
- `gt submit` -> push branches and create or update PRs with `gh`.
- `gt sync` -> refresh trunk, rebase the bottom branch, then restack upward.
- `gt log` -> summarize the local stack and PR/base relationships.

Do not try to emulate every Graphite command. `absorb`, `freeze`, `split`, `reorder`, `unlink`, and `undo` are out of scope unless the user explicitly asks for them.

## Execution Notes

- Prefer `gh pr view`, `gh pr create`, and `gh pr edit` for PR operations.
- When submitting a stack, set each PR base branch to the resolved parent branch.
- When a parent PR has already merged, retarget the child to the next surviving parent or trunk, then restack.
- If a rebase conflicts, resolve deliberately file by file. If the resolution is not clear from local context, stop and report the exact blocker.

## Output Expectations

- Report the trunk branch, current branch, resolved parent, and whether parentage was inferred or explicit.
- Report which branches were restacked, moved, pushed, or submitted.
- Include PR URLs when created or updated.
- Call out conflicts, blocked rebases, or metadata inconsistencies explicitly.
