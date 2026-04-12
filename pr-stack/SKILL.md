---
name: pr-stack
description: Manage stacked pull requests on top of plain git and GitHub CLI. Use when the user wants to create, inspect, restack, move, submit, or sync a stack of dependent branches and PRs while tracking parent branches explicitly and avoiding unsafe history rewrites.
---

# PR Stack

Use this skill for a generic stacked PR workflow built on top of plain `git` and `gh`.

## Mental Model

A stack has three parts:

- A trunk branch, usually the repository default branch.
- A chain or tree of feature branches where each branch has one intended parent.
- A matching set of pull requests whose base branch is that parent, not always trunk.

Prefer explicit local metadata over guesswork:

- Required:
- `stack.trunk=<branch>`
- `branch.<name>.stackParent=<parent-branch>`

- Strongly recommended:
- `stack.remote=<remote>`
- `branch.<name>.stackParentRemoteRef=<remote>/<parent-branch>`

Use `stack.trunk` and `branch.<name>.stackParent` as the authoritative stack metadata. Resolve PR state live through `gh` rather than storing PR metadata in branch config. Derive child branches by scanning local branches for matching `stackParent` values rather than storing child lists explicitly.

Read those values first. If required values are missing, infer a recommended parent from existing PR base branches or Git ancestry, state clearly that the result is inferred, and ask the user to confirm before any operation that would rewrite history or change PR bases.

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
- When conflicts happen during a rebase or restack, resolve them automatically when the correct resolution is clear from the local code and stack intent.

3. Treat these as the supported core operations.
- `track`: record or repair `stack.trunk` and `branch.<name>.stackParent`.
- `log`: show stack order, parent relationships, and PR status.
- `create`: create a child branch from the current branch, generate a commit message from the actual staged change, create the commit, and record the current branch as the new branch's parent.
- `restack`: rebase descendants in parent-to-child order so each branch contains its parent's tip in history.
- `move`: change a branch's parent, then restack that branch and its descendants.
- `squash`: squash the current branch into a single commit on top of its parent branch, with a new commit message derived from the full branch diff against that parent.
- `submit`: push changed branches and create or update one PR per branch with base = parent branch, and ask whether the PR title and description should be refreshed from the current branch diff before updating them.
- `sync`: fetch remote trunk, rebase the bottom branch on trunk, then restack upward.

4. Stop when ancestry is ambiguous and the next step would rewrite history or change PR bases.
- If parentage is unclear, surface the competing interpretations and ask before rebasing, moving, or retargeting PRs.
- If the configured parent conflicts with actual Git ancestry, call that out and reconcile intentionally.

## Command Shape

- Use `stack` as the command word when describing the workflow.
- `stack create` -> branch from the current branch, generate a commit message from the staged change, commit, then record parent metadata.
- `stack restack` -> ordered rebases of descendants onto their recorded parents.
- `stack move` -> update parent metadata, then restack.
- `stack squash` -> rewrite the current branch into a single commit on top of its parent branch.
- `stack submit` -> push branches and create or update PRs with `gh`, optionally refreshing PR titles and descriptions from the branch diff.
- `stack sync` -> refresh trunk, rebase the bottom branch, then restack upward.
- `stack log` -> summarize the local stack and PR/base relationships.

Do not expand the scope casually into branch surgery commands such as `absorb`, `freeze`, `split`, `reorder`, `unlink`, or `undo` unless the user explicitly asks for them.

## Execution Notes

- When creating a commit, generate the commit message from the staged diff or final squashed diff rather than from branch names or prior commit subjects.
- Prefer `gh pr view`, `gh pr create`, and `gh pr edit` for PR operations.
- When creating a PR, derive the PR title and body from the branch diff against its parent branch. If the branch contains multiple commits, evaluate them holistically and write one coherent PR summary rather than mirroring the commit list mechanically.
- When submitting a stack, set each PR base branch to the resolved parent branch.
- Before updating an existing PR during `stack submit`, ask whether the PR title and description should be refreshed to match the current branch diff. If the user says yes, regenerate them holistically from the branch changes.
- When a parent PR has already merged, retarget the child to the next surviving parent or trunk, then restack.
- If a rebase conflicts, resolve it file by file when the correct result is clear from local context, stage the resolution, and continue automatically. If the resolution is not clear, stop and report the exact blocker.

## Output Expectations

- Report the trunk branch, current branch, resolved parent, and whether parentage was inferred or explicit.
- Report which branches were restacked, moved, pushed, or submitted.
- Include PR URLs when created or updated.
- Call out conflicts, blocked rebases, or metadata inconsistencies explicitly.
