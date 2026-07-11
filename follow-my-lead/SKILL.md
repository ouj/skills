---
name: follow-my-lead
description: Continue partial code work only far enough for the relevant compile, typecheck, or existing tests to pass. Use when the user asks an agent to pick up staged, unstaged, untracked, or locally committed work and minimally repair it without completing the broader feature. Inspect the branch, identify the narrow validation target, confirm the inferred intent with one yes-or-no question, then make the smallest necessary changes while preserving unrelated work and asking before changing tests or refactoring adjacent code.
---

# Follow My Lead

Make the user’s work in progress green. Do not turn it into a complete feature.

<HARD-GATE>
Before modifying files, inspect the existing work, identify the narrow validation target, and ask the user to confirm the inferred intent.

After confirmation, proceed without generating a plan or specification. Ask again only if passing the confirmed validation target requires a material expansion of scope or an ask-first change defined below.
</HARD-GATE>

## Scope

Completion means that the confirmed compile, typecheck, or existing test target passes.

Stop when that target passes, even if the branch still contains:

- TODOs or placeholders
- Incomplete feature behavior
- Unwired optional paths
- Missing documentation
- Missing new test coverage
- Cleanup opportunities
- Other work not required by the validation target

Do not complete the broader feature unless the user explicitly requests it.

## Principles

- Treat the user’s explicit request as the strongest evidence of intent.
- Treat branch artifacts as clues, not as automatically correct.
- Make the smallest change that satisfies the confirmed validation target.
- Preserve staged, unstaged, untracked, and unrelated user changes.
- Follow failures only as far as necessary to make the target pass.
- Do not add features or perform speculative cleanup.
- Do not assume existing tests must remain unchanged; they may need to change with the confirmed intent.
- Do not weaken or remove tests merely to conceal an implementation defect.
- Distinguish failures caused by the current work from unrelated baseline failures.

## Ask-first changes

Even after the user confirms the intent, explain why the validation target cannot pass without the proposed action and ask one yes-or-no question before:

- Refactoring adjacent code
- Adding a new test
- Modifying an existing test
- Weakening or removing an existing test

State the smallest proposed change. For a test change, say explicitly whether it adds, modifies, weakens, or removes coverage. Do not bundle unrelated ask-first changes into one approval.

If approved, make only the change required by the confirmed intent and validation target.

## Workflow

### 1. Inspect the existing work

Start with:

```bash
git branch --show-current
git status --short
git diff --stat
git diff --cached --stat
git log --oneline --decorate -n 10
```

Then inspect:

- Staged and unstaged diffs
- Untracked files
- Relevant locally committed changes
- The actual changed files
- Nearby types, call sites, configuration, and existing tests

Use focused diffs when the change is large:

```bash
git diff -- path/to/file
git diff --cached -- path/to/file
```

If the working tree is clean, inspect recent branch commits and compare them with the available local base or upstream.

Do not fetch, reset, revert, or otherwise modify repository state while gathering context.

### 2. Identify the validation target

Infer the narrowest command that represents success for the current work, such as:

- A focused compile target
- A package or module typecheck
- The existing test nearest the changed code
- A targeted lint command when lint is the reported failure

Run the narrow target to establish the current failure when practical.

Do not start with the full repository test suite when a focused target is available.

### 3. Confirm intent

Summarize the intended repair and validation target in one or two sentences.

Ask one yes-or-no question:

```text
I believe the goal is to make the current async-save changes compile and pass the existing save-flow tests, without completing the broader UI work. Should I finish that minimal slice?
```

Do not present multiple-choice alternatives, a detailed implementation plan, or a specification.

If the user answers no:

- Ask another yes-or-no question when a concrete alternative is evident.
- Otherwise, ask for a brief correction and confirm the revised intent.
- Ask only one blocking question at a time.

### 4. Make the minimal repair

After confirmation:

- Fix only errors required by the validation target.
- Modify only the necessary files and interfaces.
- Propagate type or contract changes only as far as compilation requires.
- Treat test changes as eligible repairs when the confirmed intent changes expected behavior or coverage must move with the code.
- Preserve incomplete work that does not block validation.
- Avoid migrations, documentation, cleanup, and additional wiring unless the target directly requires them.

Do not request separate approval for routine fixes inside the confirmed scope.

If the repair requires an ask-first change, pause before making it. For example:

```text
The existing save-flow test asserts the old synchronous behavior. Should I update it to assert the confirmed async behavior?
```

If passing the target requires a behavior decision or materially broader work, stop and ask a concise yes-or-no question before proceeding.

### 5. Validate and stop

Rerun the confirmed validation target after each relevant repair.

Run broader checks only when:

- The confirmed target invokes them
- A focused result is insufficient to establish success
- The user explicitly requests them

Stop once the confirmed target passes.

Report unrelated or pre-existing failures instead of expanding scope to repair them.

### 6. Report the result

Keep the summary concise. Include:

- The minimal repairs made
- The validation target that now passes
- Any unrelated failures
- Intentionally unfinished work that remains outside scope

Do not describe the broader feature as complete.
