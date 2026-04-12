---
name: pr-stack
description: Manage stacked pull requests on top of plain git and GitHub CLI. Use when the user wants to create, inspect, restack, move, submit, or sync a stack of dependent branches and PRs while tracking parent branches explicitly and avoiding unsafe history rewrites.
---

# PR Stack

<<<<<<< HEAD
Use this skill for stacked PR workflows built on top of plain `git` and `gh`.
=======
Use this skill for a generic stacked PR workflow built on top of plain `git` and `gh`.
>>>>>>> aa8cf14 (Refine pr-stack guidance for generic workflow and conflict handling)

## Mental Model

A stack has:

- A trunk branch, usually the repo default branch.
- Feature branches where each branch has one intended parent.
- Pull requests whose base branch is that parent, not always trunk.

Track stack relationships explicitly:

- Required: `stack.trunk=<branch>`, `branch.<name>.stackParent=<parent-branch>`
- Strongly recommended: `stack.remote=<remote>`, `branch.<name>.stackParentRemoteRef=<remote>/<parent-branch>`

<<<<<<< HEAD
Use `stack.trunk` and `branch.<name>.stackParent` as the source of truth. Resolve PR state live through `gh`. Derive children by scanning branches for matching `stackParent` values.
=======
Read those values first. If they are missing, infer a recommended parent from existing PR base branches or Git ancestry, state clearly that the result is inferred, and ask the user to confirm before any operation that would rewrite history or change PR bases.
>>>>>>> aa8cf14 (Refine pr-stack guidance for generic workflow and conflict handling)

If required metadata is missing, infer a recommended parent from Git ancestry or existing PR bases, mark it as inferred, and ask the user to confirm before any operation that would rewrite history or change PR bases.

## Rules

<<<<<<< HEAD
- Resolve state first: inspect `git status -sb`, current branch, trunk, and a concise branch graph; confirm `gh` auth for PR operations; never operate on detached HEAD.
- Protect history: never rewrite the default branch, never use plain `--force`, and check for existing PRs before creating new ones.
- Handle dirty trees deliberately: if the worktree is dirty, decide whether branch switching or rebasing is safe before continuing.
- Resolve conflicts automatically when the correct result is clear from local context and stack intent; otherwise stop and report the blocker.
- Treat merged parents specially: retarget the child to the next surviving parent or trunk, then restack.
- Keep scope tight: do not expand casually into commands such as `absorb`, `freeze`, `split`, `reorder`, `unlink`, or `undo`.

## Commands
=======
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
>>>>>>> aa8cf14 (Refine pr-stack guidance for generic workflow and conflict handling)

- `stack track`: record or repair `stack.trunk` and `branch.<name>.stackParent`.
- `stack log`: show stack order, parent relationships, and live PR status.
- `stack create`: create a child branch from the current branch, generate the commit message from the staged diff, create the commit, and record the current branch as parent.
- `stack restack`: rebase descendants in parent-to-child order so each branch includes its parent tip.
- `stack move`: change a branch's parent, then restack that branch and its descendants.
- `stack squash`: rewrite the current branch into one commit on top of its parent, with a commit message derived from the full branch diff against that parent.
- `stack submit`: push changed branches, create or update one PR per branch with base = parent branch, and ask whether PR title and description should be refreshed before updating them.
- `stack sync`: fetch remote trunk, rebase the bottom branch on trunk, then restack upward.

<<<<<<< HEAD
## Content Generation

- Generate commit messages from the staged diff or squashed branch diff, not from branch names or prior commit subjects.
- Generate PR title and body from the branch diff against the parent branch.
- If the branch has multiple commits, summarize the branch holistically instead of mirroring the commit list.
- During `stack submit`, set each PR base branch to the resolved parent branch.
- Before updating an existing PR during `stack submit`, ask whether the PR title and description should be refreshed; if yes, regenerate them from the current branch diff.
=======
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
>>>>>>> aa8cf14 (Refine pr-stack guidance for generic workflow and conflict handling)

## Output Expectations

- Report the trunk branch, current branch, resolved parent, and whether parentage was explicit or inferred.
- Report which branches were restacked, squashed, moved, pushed, or submitted.
- Include PR URLs when created or updated.
- Call out conflicts, blocked rebases, or metadata inconsistencies explicitly.
