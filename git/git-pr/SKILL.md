---
name: git-pr
description: Create or update a GitHub pull request for the current branch safely. Use when the user wants to open or refresh a PR, avoid duplicate PRs, handle stacked branches, or derive PR title and body from the branch diff without casually rewriting history.
---

# Git PR

Use this skill for pull request creation or update work on the current branch.

## Workflow

1. Resolve the branch context.
- Never open a pull request from the default branch.
- If the current branch is the default branch, create or switch to a feature branch before PR work.
- Do not create commits unless the user explicitly asks.

2. Ensure the branch is publishable.
- Confirm the current branch name and remote.
- Ensure the branch is pushed before PR operations.
- Use `-u` when setting upstream for a new branch.

3. Check for an existing pull request first.
- Look for an open PR for the current branch before creating anything new.
- If one exists, update its title and body instead of creating a duplicate.

4. Choose the correct base branch.
- Default to the remote default branch.
- For stacked work, use the parent feature branch as the base when that dependency is clear.
- If stacked ancestry is unclear, ask before choosing a non-default base.

5. Build the PR content from the actual diff.
- Derive the title and body from the diff between the current branch and the selected base.
- Describe what changed, why it changed, and the user or developer impact.
- Mention validation that was run when known.

6. Avoid history surprises.
- Never pull, rebase, reset, or force-push unless the user explicitly requests or approves it.
- If the branch must move because the parent PR changed, explain the tradeoff before acting.

7. Use the best available GitHub path.
- Prefer a connected GitHub app when available.
- Fall back to `gh` when branch-to-PR lookup or PR creation is easier through CLI.

## Output expectations

- Report whether the PR was created or updated.
- Include the final title, base branch, head branch, and PR URL.
- Call out any unresolved questions about stack order or history.
