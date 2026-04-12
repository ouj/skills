---
name: git-sync
description: Sync a feature branch with the latest remote default branch using fetch, rebase, conflict resolution, and safe force-push. Use when the user wants to rebase their branch onto the newest `origin/main` or equivalent without merging and with explicit handling of conflicts and push safety.
---

# Git Sync

Use this skill when the user wants to update an existing feature branch on top of the latest remote default branch.

## Workflow

1. Confirm this is a branch-sync task.
- Fetch remote updates first.
- Resolve the remote default branch if it is not explicitly known.
- If the user is on the default branch, do not rebase and force-push it as though it were a feature branch. Clarify intent or switch to a feature branch first.

2. Rebase onto the latest remote base.
- Rebase the current feature branch onto the remote default branch, usually `origin/main`.
- Do not replace this with a merge unless the user asks for merge behavior.

3. Handle conflicts deliberately.
- If conflicts occur, resolve them file by file, stage the resolutions, and continue the rebase.
- If any conflict cannot be resolved safely from the available context, stop and report the exact blocker.

4. Publish safely after a rewritten history.
- Force-push with `--force-with-lease`.
- Do not use a plain `--force` unless the user explicitly asks.

## Output expectations

- Report the base branch used, whether conflicts occurred, how they were resolved, and the push result.
- If the operation stops early, report the exact file or decision that blocked completion.
