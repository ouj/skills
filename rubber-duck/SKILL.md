---
name: rubber-duck
description: Design-first collaboration for coding changes, bug fixes, system design, and interface changes. Use when the user asks to rubber-duck a problem, think through a change before coding, compare solution approaches, or produce a design or specification before implementation. Inspect existing context, clarify consequential decisions, define interfaces and boundaries, and require explicit design approval before implementation.
---

# Rubber Duck

Turn an ambiguous change into a minimal, coherent design grounded in the existing system.

<HARD-GATE>
Do not modify code, scaffold implementation files, or take implementation action until the user explicitly approves the proposed design. Read-only exploration and writing a design document are allowed. Treat code snippets as illustrative contracts only.

After approval, implement only if the user has requested implementation, either initially or after reviewing the design. Otherwise, stop at the approved specification.
</HARD-GATE>

## Principles

- Prefer the smallest change that cleanly solves the stated problem.
- Preserve existing patterns unless they materially obstruct the design.
- Make boundaries, dependencies, and interfaces explicit.
- Apply YAGNI; exclude unrelated cases and refactors.
- Scale the ceremony to the complexity of the change.
- Exclude testing, rollout, metrics, monitoring, and operational planning unless the user requests them.

## Workflow

### 1. Inspect the context

- Inspect relevant code, documentation, interfaces, and configuration.
- Inspect recent history only when it helps explain the current design or constraints.
- If no project context exists, state the assumptions being used.
- Summarize the relevant components, boundaries, constraints, and patterns worth preserving.
- Narrow an overly broad request to one coherent design slice.

### 2. Clarify consequential decisions

- Ask only questions whose answers could materially change the design.
- Ask one question at a time.
- Do not ask questions that project exploration can answer.
- Use the host's structured-input tool when available.
- Otherwise, provide lettered choices with the recommended option first and accept free-form input.

Use this chat format:

A. Recommended — option and short reason  
B. Alternative — trade-off  
C. Alternative — trade-off

### 3. Explain the current system

- Identify the change surfaces and system boundaries before proposing solutions.
- Use a “before” diagram only when multiple components or interactions would be clearer visually.

### 4. Compare approaches

Present two or three materially distinct, viable approaches. If only one approach is credible, say so instead of inventing alternatives.

For each approach, explain:

- What changes
- Why it is attractive
- What it complicates
- How it affects existing interfaces or workflows

Lead with the recommended approach and allow the user to suggest another direction.

### 5. Define the proposed design

After the user selects or accepts a direction:

- Describe responsibilities, boundaries, dependencies, and user-visible behavior.
- Call out interface changes explicitly, including relevant UI states, APIs, props, events, routes, schemas, configuration, CLI flags, or cross-service messages.
- Use small, clearly marked illustrative snippets when they clarify a contract.
- Use an “after” diagram when it materially clarifies a multi-component interaction.

For complex designs, align on uncertain sections incrementally. Avoid repeated approval checkpoints when one final review is sufficient.

### 6. Write and review the specification

Produce a concise specification containing only the sections needed from:

- Problem
- Goals and non-goals
- Current state
- Approaches considered
- Recommended design
- Before and after diagrams
- Interface changes
- Open questions and trade-offs

Before presenting it:

- Remove placeholders and contradictions.
- Replace vague terms with observable behavior.
- Verify that the scope matches the selected approach.
- Verify that every interface change is explicit.
- Remove implementation and delivery detail that the user did not request.

Present the specification in chat unless the user requests a file.

### 7. Request approval

Ask the user to approve the final design or identify changes.

Do not begin implementation without explicit approval. If the user already approved a materially identical final design, do not require redundant approval.