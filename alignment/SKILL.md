---
name: alignment
description: Help the user and AI align on a coding problem before implementation. Use when the user wants to turn a rough feature, bug fix, refactor, or architecture idea into a concrete design/spec through collaborative dialogue; when the work requires codebase exploration, clarifying questions, approach comparison, system diagrams, change-surface mapping, interface planning, or explicit design approval before any code is written.
---

# Alignment

Help turn coding ideas into approved designs and specs through natural collaborative dialogue.

<HARD-GATE>
Do NOT invoke any implementation skill, write any code, scaffold any project, or take any implementation action until you have:
1. Explored the current project context
2. Presented the design
3. Received explicit user approval

This applies to every project regardless of perceived simplicity.
</HARD-GATE>

## Workflow

Complete these steps in order:

1. Explore project context
2. Ask clarifying questions
3. Propose 2-3 approaches
4. Align on the current system and change surfaces
5. Align on the target system interaction and interface changes
6. Write the design doc in a folder specified by the user
7. Self-review the spec for gaps and ambiguity

## 1. Explore Project Context

- Inspect the relevant files, docs, config, tests, and recent commits before asking detailed questions.
- Summarize only the context that affects the design.
- If a question can be answered by exploring the codebase, inspect first instead of asking.
- Follow existing patterns in the repo rather than inventing a new structure without evidence.
- If the request actually describes multiple independent systems, say so early and help decompose the work before refining details.

## 2. Ask Clarifying Questions

- Ask one question at a time.
- Focus on purpose, constraints, success criteria, non-goals, and failure modes.
- Prefer concise multiple-choice questions when they reduce ambiguity, but use open-ended questions when nuance matters.
- When presenting multiple-choice options, index them with lowercase letters: `a.`, `b.`, `c.`, `d.`
- Do not ask for information that can be discovered from the project context.
- Keep moving toward a design decision; avoid broad brainstorming that does not narrow the problem.

## 3. Propose 2-3 Approaches

- Once you understand the problem well enough, propose 2-3 viable approaches.
- For each approach, include the core idea, main trade-offs, key risks, and expected change scope.
- Lead with your recommended approach and explain why it is the best fit for the current codebase and constraints.
- Prefer pragmatic options that preserve clarity, isolation, and maintainability.

## 4. Align on the Current System

- Identify the systems, modules, interfaces, and boundaries involved before any changes are made.
- Call out the change surfaces explicitly: files, services, endpoints, data models, jobs, UI surfaces, configuration, tests, and docs.
- Draw a diagram that explains how the current system works before the change. Use Mermaid when supported.
- Separate confirmed facts from assumptions.

## 5. Align on the Target Design

- Show how the proposed system interaction changes after the design is applied.
- Explain what stays the same and what changes.
- Call out interface changes clearly: UI states, API contracts, CLI flags, schemas, events, or internal boundaries.
- Draw the target interaction diagram so the user can compare before and after.
- Present the design in sections scaled to complexity and get explicit approval before moving on.

## 6. Write the Design Doc

- Only write the design doc after the user approves the design.
- Ask which folder to use for the design doc. If the user already specified the folder, use it.
- Keep the document concise but complete enough to guide implementation.
- Scale the document to the size of the change. Small changes can be short; larger changes need more structure.
- Include, when relevant:
  - Problem and goals
  - Non-goals
  - Relevant project context
  - Options considered
  - Recommended approach
  - Change surfaces
  - Current and target system diagrams
  - Interface or contract changes
  - Risks, open questions, and validation plan
- Do not start implementation after writing the doc. Wait for a separate user instruction to proceed.

## 7. Spec Self-Review

Do a quick inline review of the finished spec and fix or flag:

- Placeholders, TODOs, or incomplete sections
- Contradictions between sections
- Ambiguous terms or unstated assumptions
- Scope creep or missing non-goals
- Missing change surfaces or interface details
- Open questions that would block implementation

End with a short readiness statement that says whether the spec is ready for implementation and what, if anything, is still unresolved.

## Working Style

- Stay collaborative and conversational.
- Keep the user moving through the decision process without overwhelming them.
- Use concrete references to the codebase when grounding the design.
- Prefer diagrams and explicit interface descriptions over vague prose.
- Return to clarification if new ambiguity appears while presenting the design.
