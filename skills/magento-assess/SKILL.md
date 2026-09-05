---
name: magento-assess
description: Synthesize one human-reviewed Magento discovery handoff into a compact private technical diagnostic assessment for a human scope decision.
argument-hint: "[approved assessment objective and discovery handoff]"
disable-model-invocation: true
disallowed-tools:
  - Agent
  - Artifact
  - AskUserQuestion
  - Bash
  - CronCreate
  - CronDelete
  - CronList
  - Edit
  - EnterPlanMode
  - EnterWorktree
  - ExitPlanMode
  - ExitWorktree
  - Glob
  - Grep
  - ListAgents
  - ListMcpResourcesTool
  - LSP
  - Monitor
  - NotebookEdit
  - PowerShell
  - PushNotification
  - Read
  - ReadMcpResourceTool
  - RemoteTrigger
  - ReportFindings
  - ScheduleWakeup
  - SendMessage
  - SendUserFile
  - ShareOnboardingGuide
  - Skill
  - TaskCreate
  - TaskGet
  - TaskList
  - TaskOutput
  - TaskStop
  - TaskUpdate
  - TodoWrite
  - ToolSearch
  - WaitForMcpServers
  - WebFetch
  - WebSearch
  - Workflow
  - Write
model: sonnet
effort: medium
---

# Magento Assess

Use only `$ARGUMENTS`. Respond in English. Preserve identifiers, paths, commands, product names, evidence references, and classifications exactly where their precision matters.

## Assessment boundary

This Skill performs terminal synthesis of one supplied, human-reviewed `magento-discover` handoff. It does not inspect a repository or replace human judgment. Ignore repository context, `CLAUDE.md`, memory files, previous conversations, and unstated knowledge.

Do not read files, run commands, inspect repositories, use Git or GitHub, call Skills or agents, write files, create artifacts, access external systems, or expose secrets. Do not propose implementation details, code changes, tests to execute, or a committed implementation scope. The response is a private assessment draft for the user to save outside this public toolkit.

The complete named `disallowed-tools` list makes reviewed built-in tools unavailable only during the turn that invokes this Skill, subject to the session's baseline behavior. It does not semantically validate the input or enforce evidence discipline. Input validity, synthesis quality, evidence classification, and the stop condition remain prompt- and human-enforced. A later user message clears the Skill-local restrictions.

## 1. Input gate

Before producing an assessment, require all of these non-empty fields in `$ARGUMENTS`:

- `ASSESSMENT APPROVED: yes`
- `ASSESSMENT OBJECTIVE`
- `DISCOVERY HANDOFF`

`DISCOVERY HANDOFF` must contain one complete, human-reviewed `magento-discover` report with an `Outcome` of exactly `Complete` or `Partial` and all canonical headings:

- `Discovery Status`
- `Executive Summary`
- `Architecture and Execution Flow`
- `Evidence`
- `Hypotheses`
- `Findings`
- `Constraints and Limitations`
- `Evidence Gaps and Validation Needed`
- `Recommended Next Stage`

Treat the input as invalid if a required field or canonical heading is empty, approval is not exactly `yes`, the outcome is absent or invalid, the handoff contains `Discovery Blocked`, or the handoff is otherwise unusable for the stated objective.

For any invalid input, return only:

```markdown
## Assessment Blocked

- Missing or invalid input:
- Suggested action:
```

Identify only what must be corrected and the action needed to correct it. Do not analyze the task, infer facts, summarize partial content, or use tools.

## 2. Synthesis rules

- Synthesize only the supplied discovery handoff for the stated assessment objective.
- Preserve its evidence references, classifications, inspected scope, excluded scope, constraints, conflicts, limitations, and uncertainty.
- Preserve material uncertainty and gaps from the handoff. Do not add a severity, root cause, risk, absence claim, remediation fact, runtime conclusion, business impact, framework behavior, configuration value, or validation result not explicitly supported by the handoff. If severity is unsupported, use `Unknown`.
- Separate verified local code facts from operational risk and runtime uncertainty.
- Include only evidence-backed risks or explicit unknowns. Do not add content to fill a section; use `None identified from the supplied handoff` where appropriate.
- Do not quote source code or reproduce secrets. Prefer evidence references. Consolidate duplicate evidence only when every original reference remains traceable.
- Progressive remediation may contain only conditional, decision-level directions, ordered from evidence confirmation through stabilization, resilience, and optional modernization.
- Do not recommend or initiate another Skill or model route. Stop after the assessment and require a human scope decision.

### Finding output mapping

- The classification of every supplied discovery `Finding` is authoritative. Preserve it exactly in the assessment.
- A verified `Evidence` item may explain a finding but must never upgrade that finding from `Potential`, `Technical debt`, or `Unknown` to `Verified`.
- Do not create a new assessment finding solely from a supplied `Evidence` item or `Hypothesis` when the discovery handoff did not present it as a `Finding`.
- Evidence-only local code facts may be summarized in `Architecture`, `Execution Flow`, `Evidence and Limitations`, or `Security and Operational Risks`, with their material uncertainty preserved. Do not convert them into a new finding classification.
- Represent every material supplied discovery `Finding` exactly once in the area selected by its supplied classification:
  - `Verified` → Section 7, `Verified Findings`.
  - `Potential` or `Unknown` → Section 8, `Potential Findings to Validate`.
  - `Technical debt` → Section 10, `Technical Debt`.
- Preserve the supplied evidence references for every represented finding.
- A source finding phrased as `Verified code fact; runtime impact Potential` remains `Verified` only for the local code fact. Preserve its impact or reachability as `Potential` or `Unknown`.
- Use exactly one classification label per assessment finding: `Verified`, `Potential`, `Technical debt`, or `Unknown`. Never output combined labels such as `Potential / Unknown`.
- A hypothesis status (`Supported`, `Contradicted`, or `Inconclusive`) is not a finding classification and must not be silently converted into one. `Supported` does not establish root cause.

## 3. Output

Produce the following structure exactly. Do not add top-level report headings. Keep it compact and evidence-led. Omit empty placeholder prose, but retain every numbered heading and its named subsections.

```markdown
# Technical Diagnostic Assessment

**Template version:** `0.1.0`
**Assessment version:** Unknown unless supplied
**Date:** Unknown unless supplied
**Scope:** <scope preserved from the handoff>
**Status:** Draft

## 1. Executive Summary

## 2. Objective and Constraints

### Objective

### Constraints

### Non-Goals

## 3. Evidence and Limitations

- Supplied handoff: Human-reviewed magento-discover report; outcome <Complete | Partial>
- Scope inspected:
- Material gaps:

Classification meanings: `Verified` is directly supported by inspected local evidence; `Potential` is plausible but unestablished; `Technical debt` is a maintainability or compatibility concern without proof of a functional defect; `Unknown` cannot be resolved from the supplied handoff.

## 4. Business Purpose

## 5. Current Architecture

### Entry Points

### Authentication and Authorization

### Application Services and Domain Logic

### Persistence and State

### Scheduled or Asynchronous Processing

### CLI and Administrative Operations

### External Dependencies

## 6. Execution Flow

## 7. Verified Findings

### [Severity or Unknown] — <finding title>

- Classification: Verified
- Evidence references:
- Local code fact:
- Operational or runtime uncertainty:

## 8. Potential Findings to Validate

### [Severity or Unknown] — <finding title>

- Classification: <exactly one supplied classification>
- Evidence references:
- Evidence still required:
- Potential impact: Unknown unless supported

## 9. Security and Operational Risks

## 10. Technical Debt

### [Severity or Unknown] — <finding title>

- Classification: Technical debt
- Evidence references:
- Maintainability or compatibility concern:

## 11. Compatibility and Business Constraints

## 12. Progressive Remediation

### Phase 0 — Confirm Diagnosis

### Phase 1 — Stabilization

### Phase 2 — Security and Resilience

### Phase 3 — Internal Modernization

### Phase 4 — Optional Architecture Evaluation

## 13. Testing Strategy

### Characterization Tests

### Regression Tests

### Integration Tests

### Operational Validation

## 14. Questions Before Scope Confirmation

## 15. Prioritized Roadmap

| Priority | Decision-level direction | Reason | Dependency |
|---|---|---|---|

## 16. Recommendation
```

Apply these section rules:

- `Status` must be `Draft`; the supplied `magento-discover` handoff is static discovery, not validation.
- `Evidence and Limitations` must identify the supplied handoff, its inspected scope, and every material gap relevant to the objective.
- `Security and Operational Risks` must contain only applicable evidence-backed risks or explicit unknowns. Otherwise write `None identified from the supplied handoff`.
- `Technical Debt` must preserve that classification and must not present maintainability or compatibility concerns as verified defects.
- `Progressive Remediation` must remain conditional and decision-level. Use `None identified from the supplied handoff` for an inapplicable phase.
- `Testing Strategy` must state that this assessment performed no tests or runtime validation. If the handoff records tests or runtime validation, report them only as supplied evidence and do not claim this Skill performed them. Do not prescribe tests to execute.
- `Prioritized Roadmap` must not invent priorities. Use only supported priorities or `Unknown`, and keep actions at decision level.
- `Recommendation` must end with a human scope-decision checkpoint. It must not claim authorization, implementation, validation, or a model-routing change.

Before responding, silently confirm that:

- the input gate passed;
- every claim is traceable to `$ARGUMENTS`;
- every assessment finding maps back to exactly one supplied discovery `Finding`;
- each assessment finding's classification exactly matches its supplied discovery `Finding` classification;
- every material supplied discovery `Finding` appears exactly once in the required classification area;
- every represented finding preserves its supplied evidence references;
- no `Evidence`-only item or `Hypothesis` became a new assessment finding;
- no combined finding classification labels remain;
- unsupported severity is `Unknown`;
- no material uncertainty or gap was removed and no missing fact was invented; and
- the response stops at human scope decision.
