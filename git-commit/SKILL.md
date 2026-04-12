---
name: git-commit
description: Create intentional local git commits safely. Use when the user wants to commit current changes without pushing, especially when the worktree may be mixed, they want a Conventional Commit message, or they want protection against committing on the default branch and staging unrelated files.
---

# Git Commit

Use this skill for local commit work only.

## Workflow

1. Inspect scope first.
- Run `git status -sb` and inspect the relevant diff before staging anything.
- If the worktree contains unrelated changes, ask which files belong in scope.

2. Protect the default branch.
- Never commit directly on the local default branch such as `main` or `master`.
- If the user is on the default branch, create a descriptive feature branch first.

3. Stage conservatively.
- Auto-stage modified tracked files only when the whole tracked worktree is clearly in scope.
- Do not stage deleted files without explicit confirmation.
- Do not stage untracked files without explicit confirmation.
- Prefer explicit `git add <path>` over broad staging when the scope is mixed.

4. Write the commit intentionally.
- Derive the message from the staged diff, not from the unstaged worktree.
- Use a Conventional Commit message.
- Keep the subject concise and specific to the staged change.

5. Stop at the local commit.
- Do not push.
- Do not open or update a pull request.

## Output expectations

- Report the branch used, what was staged, and the resulting commit hash.
- If the worktree is mixed or ambiguous, surface that clearly before committing.
