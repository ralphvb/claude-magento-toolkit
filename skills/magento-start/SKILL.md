---
name: magento-start
description: Classify and normalize a Magento Open Source or Adobe Commerce request, identify blocking gaps, and recommend one next stage.
argument-hint: "[task description]"
disable-model-invocation: true
model: sonnet
effort: medium
---

# Magento Start

Use `$ARGUMENTS` as the request. Respond in English. Preserve identifiers, paths, commands, and product names exactly as provided. If `$ARGUMENTS` is empty, ask for the task and stop.

## Intake boundary

This Skill performs intake only. Do not read files, inspect the repository, run tools or commands, modify anything, invoke Skills or agents, use Git/GitHub, or start discovery, planning, testing, or implementation.

Treat only `$ARGUMENTS` and information explicitly supplied in this invocation as factual input. Ignore `CLAUDE.md`, Memory files, repository summaries, previous conversations, and previously discovered information in every output section.

Every path, file, class, command, configuration, business rule, and implementation mechanism must be traceable to `$ARGUMENTS`. Otherwise omit it. Never turn inherited context or Magento conventions into facts, hypotheses, validation, non-goals, reasoning, or suggested actions.

## 1. Classify

Choose exactly one mode matching the deliverable:

- `DIAGNOSTIC`: current behavior, cause, or scope must be discovered.
- `BUGFIX`: observed and expected behavior differ and a correction is requested.
- `FEATURE`: new business behavior is requested.
- `REFACTOR`: structure should improve without changing behavior.
- `MECHANICAL`: the change is repetitive, low-ambiguity, and deterministically verifiable.

Mention secondary concerns without creating parallel workflows.

## 2. Normalize

Produce: `MODE`, `TASK`, `BUSINESS CONTEXT`, `KNOWN SYMPTOMS`, `SCOPE`, `KNOWN FACTS`, `HYPOTHESES TO VERIFY`, `CONSTRAINTS`, `ACCEPTANCE CRITERIA`, `EXPECTED DELIVERABLE`, `VALIDATION`, `NON-GOALS`, and `OPEN QUESTIONS`.

Rules:

- preserve intent and separate facts from hypotheses;
- use `Not provided` for missing information;
- preserve a named scope without resolving or expanding it;
- include only user-provided hypotheses; otherwise use `Not provided`;
- add the intake-only boundary to `CONSTRAINTS`, but invent no project constraint;
- derive only acceptance criteria that restate the requested outcome, prefixed with `Derived from task:`;
- make no assumption that could change scope or behavior;
- avoid repetition and optional follow-up work.

Use `Open questions: None` when no relevant question remains.

## 3. Blocking gaps

A gap is blocking only if its answer could materially change the mode, authorized scope, expected behavior, compatibility, security/data handling, acceptance criteria, or deliverable. Ask at most three questions.

If blocked:

- list one to three concise questions under `Blocking Gaps`;
- use `Complete intake` as the stage;
- use `No change` as both the model and effort;
- explain that required information could materially change the work;
- use `Not applicable until intake is complete.` as the routing gate;
- use exactly this suggested action: `Answer the blocking questions above, then rerun /magento-start with the completed request.`;
- stop without applying the routing rules in section 4.

Otherwise write `Blocking gaps: None` and continue to section 4.

## 4. Next stage and routing

Recommend exactly one stage:

- `DIAGNOSTIC` → bounded read-only discovery;
- `BUGFIX` → reproduction, or bounded discovery when the cause is unknown;
- `FEATURE` → lightweight specification;
- `REFACTOR` → characterization of current behavior;
- `MECHANICAL` → bounded transformation with deterministic validation.

Choose the least expensive reliable route:

- bounded discovery, normal implementation, tests → `Sonnet`, `Medium`;
- difficult debugging, high ambiguity, independent review → `Sonnet`, `High`;
- cross-cutting architecture, critical integrations, high-risk legacy decisions → `Opus`, `High`;
- mechanical deterministic work → `Haiku`, `Low`.

The routing reason may mention only stated scope, ambiguity, risk, and work type. Model and effort are recommendations, not confirmed runtime state. Never claim this Skill changed them.

Use this routing gate exactly, replacing only the placeholders:

```text
Verify that the active session uses <MODEL> with <EFFORT> effort. If it does not, change it manually before approving the next stage.
```

Use exactly one suggested action without replacing or appending text:

- discovery: `After confirming the routing gate, run /magento-discover using exactly the Scope stated above. Do not expand or resolve the scope during intake.`
- reproduction: `After confirming the routing gate, reproduce the reported behavior using exactly the Scope and Acceptance Criteria stated above.`
- specification: `After confirming the routing gate, begin lightweight specification using exactly the Normalized Request stated above.`
- characterization: `After confirming the routing gate, characterize current behavior using exactly the Scope and Acceptance Criteria stated above.`
- transformation: `After confirming the routing gate, perform the bounded transformation using exactly the Scope and Validation stated above.`

## 5. Output

Use exactly this structure:

```markdown
## Intake Classification

- Mode:
- Confidence:
- Rationale:

## Normalized Request

### Mode
### Task
### Business Context
### Known Symptoms
### Scope
### Known Facts
### Hypotheses to Verify
### Constraints
### Acceptance Criteria
### Expected Deliverable
### Validation
### Non-Goals
### Open Questions

## Blocking Gaps

## Recommended Next Stage

- Stage:
- Model:
- Effort:
- Reason:
- Routing gate:
- Suggested action:
```

Use `High`, `Medium`, or `Low` confidence. Keep the response under approximately 400 words unless the user requests more. Stop after the recommendation and wait for confirmation.
