---
name: magento-start
description: Classify and normalize a Magento Open Source or Adobe Commerce work request, identify material gaps, and recommend one controlled next stage.
argument-hint: "[task description]"
disable-model-invocation: true
model: sonnet
effort: medium
---

# Magento Start

Use `$ARGUMENTS` as the initial work request.

Always respond in English, regardless of the language used in the request. Preserve code identifiers, paths, commands, and product names exactly as provided.

## Boundary

This Skill performs intake only.

Do not:

- inspect the repository or read files;
- modify files;
- run commands or tools;
- invoke another Skill or subagent;
- perform Git or GitHub operations;
- begin discovery, planning, testing, or implementation;
- invent project facts, paths, commands, requirements, or business rules;
- treat hypotheses as verified findings;
- request or reproduce credentials, secrets, or private customer data.

If `$ARGUMENTS` is empty, ask the user to describe the task and stop.

## 1. Classify the request

Choose exactly one primary mode:

- `DIAGNOSTIC`: the cause, current behavior, or implementation scope is not sufficiently known.
- `BUGFIX`: expected and observed behavior differ and a correction is requested.
- `FEATURE`: new business behavior is requested.
- `REFACTOR`: internal structure should improve while preserving behavior.
- `MECHANICAL`: the transformation is bounded, repetitive, low-ambiguity, and deterministically verifiable.

If more than one mode appears relevant, select the mode matching the immediate requested deliverable. Mention secondary concerns without creating additional workflows.

## 2. Normalize the intake

Produce these fields:

```text
MODE
TASK
BUSINESS CONTEXT
KNOWN SYMPTOMS
SCOPE
KNOWN FACTS
HYPOTHESES TO VERIFY
CONSTRAINTS
ACCEPTANCE CRITERIA
EXPECTED DELIVERABLE
VALIDATION
NON-GOALS
OPEN QUESTIONS
```

Rules:

- preserve the user's intent;
- separate verified facts from hypotheses;
- use `Not provided` when missing information should remain visible;
- do not infer requirements that could change behavior or scope;
- keep each section concise;
- do not repeat the same information across sections.

## 3. Identify blocking gaps

A gap is blocking only when its answer could materially change:

- the primary mode;
- authorized scope;
- expected behavior;
- external compatibility;
- security or data handling;
- acceptance criteria;
- the expected deliverable.

Ask no more than three focused questions.

If blocking information is missing, recommend `Complete intake` as the next stage and stop.

If no blocking answer is required, write:

```text
Blocking gaps: None
```

Keep non-blocking uncertainty under `OPEN QUESTIONS`.

## 4. Recommend one next stage

Recommend exactly one stage:

- `DIAGNOSTIC` → bounded, read-only discovery;
- `BUGFIX` → defect reproduction, or targeted discovery if the cause is unknown;
- `FEATURE` → lightweight specification;
- `REFACTOR` → characterization of current behavior;
- `MECHANICAL` → bounded transformation with deterministic validation.

When `magento-discover` is appropriate, recommend `/magento-discover` but do not invoke it.

Recommend a model and effort level for that next stage:

- bounded discovery, normal implementation, and ordinary tests → `Sonnet`, `Medium`;
- difficult debugging, high-ambiguity analysis, or independent review → `Sonnet`, `High`;
- cross-cutting architecture, critical integrations, or high-risk legacy decisions → `Opus`, `High`;
- bounded mechanical work with deterministic validation → `Haiku`, `Low`.

Use the least expensive route that can reliably perform the work. State a one-sentence reason when recommending `Opus`.

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
- Suggested action:
```

Use `High`, `Medium`, or `Low` for classification confidence.

Keep the complete response under approximately 500 words unless the user explicitly requests more detail.

After presenting the normalized intake and one recommended next stage, stop and wait for user confirmation.
