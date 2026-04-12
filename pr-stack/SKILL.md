---
name: pr-stack
description: Manage stacked pull requests on top of plain git and GitHub CLI. Use when the user wants to create, inspect, restack, move, submit, or sync a stack of dependent branches and PRs while tracking parent branches explicitly and avoiding unsafe history rewrites.
---

# PR Stack

Use this skill for stacked PR workflows built on top of plain `git` and `gh`.

## Mental Model

A stack has:

- A trunk branch, usually the repo default branch.
- Feature branches where each branch has one intended parent.
- Pull requests whose base branch is that parent, not always trunk.

Track stack relationships explicitly:

- Required: `stack.trunk=<branch>`, `branch.<name>.stackParent=<parent-branch>`
- Strongly recommended: `stack.remote=<remote>`, `branch.<name>.stackParentRemoteRef=<remote>/<parent-branch>`

Use `stack.trunk` and `branch.<name>.stackParent` as the source of truth. Resolve PR state live through `gh`. Derive children by scanning branches for matching `stackParent` values.

If required metadata is missing, infer a recommended parent from Git ancestry or existing PR bases, mark it as inferred, and ask the user to confirm before any operation that would rewrite history or change PR bases.

## Rules

- Resolve state first: inspect `git status -sb`, current branch, trunk, and a concise branch graph; confirm `gh` auth for PR operations; never operate on detached HEAD.
- Protect history: never rewrite the default branch, never use plain `--force`, and check for existing PRs before creating new ones.
- Handle dirty trees deliberately: if the worktree is dirty, decide whether branch switching or rebasing is safe before continuing.
- Resolve conflicts automatically when the correct result is clear from local context and stack intent; otherwise stop and report the blocker.
- Treat merged parents specially: retarget the child to the next surviving parent or trunk, then restack.
- Keep scope tight: do not expand casually into commands such as `absorb`, `freeze`, `split`, `reorder`, `unlink`, or `undo`.

## Commands

- `stack track`: record or repair `stack.trunk` and `branch.<name>.stackParent`.
- `stack log`: show stack order, parent relationships, and live PR status.
- `stack create`: create a child branch from the current branch, generate the commit message from the staged diff, create the commit, and record the current branch as parent.
- `stack restack`: rebase descendants in parent-to-child order so each branch includes its parent tip.
- `stack move`: change a branch's parent, then restack that branch and its descendants.
- `stack squash`: rewrite the current branch into one commit on top of its parent, with a commit message derived from the full branch diff against that parent.
- `stack submit`: push changed branches, create or update one PR per branch with base = parent branch, and ask whether PR title and description should be refreshed before updating them.
- `stack sync`: fetch remote trunk, rebase the bottom branch on trunk, then restack upward.

## Content Generation

- Generate commit messages from the staged diff or squashed branch diff, not from branch names or prior commit subjects.
- Generate PR title and body from the branch diff against the parent branch.
- If the branch has multiple commits, summarize the branch holistically instead of mirroring the commit list.
- During `stack submit`, set each PR base branch to the resolved parent branch.
- Before updating an existing PR during `stack submit`, ask whether the PR title and description should be refreshed; if yes, regenerate them from the current branch diff.

## Output Expectations

- Report the trunk branch, current branch, resolved parent, and whether parentage was explicit or inferred.
- Report which branches were restacked, squashed, moved, pushed, or submitted.
- Include PR URLs when created or updated.
- Call out conflicts, blocked rebases, or metadata inconsistencies explicitly.
