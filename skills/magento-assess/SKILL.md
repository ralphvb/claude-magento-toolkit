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

Objective and handoff intent must be compatible. If `ASSESSMENT OBJECTIVE` explicitly requests `INTERNAL_MODERNIZATION`, an `Internal Modernization Posture`, or a modernization decision, require a human-reviewed discovery handoff with explicit `REVIEW INTENT: INTERNAL_MODERNIZATION` and non-empty `MODERNIZATION OBJECTIVE`, `MODERNIZATION SCOPE`, and `MODERNIZATION DIMENSIONS`. A `REVIEW INTENT: STANDARD` handoff, a handoff with no explicit `REVIEW INTENT: INTERNAL_MODERNIZATION`, or a modernization handoff missing any of those fields is invalid for that objective. Identify the mismatch and instruct the user to supply a human-reviewed `INTERNAL_MODERNIZATION` discovery handoff. Do not infer modernization intent from technical-debt findings alone. Do not apply this compatibility rule when the assessment objective is standard; a standard objective with a `REVIEW INTENT: STANDARD` handoff remains valid.

If the handoff's normalized `REVIEW INTENT` is `INTERNAL_MODERNIZATION`, also require non-empty `MODERNIZATION OBJECTIVE`, `MODERNIZATION SCOPE`, and `MODERNIZATION DIMENSIONS` from the approved discovery report. Do not infer the handoff's modernization intent from technical-debt findings or from an assessment objective when the handoff does not explicitly declare that intent.

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
- Associate a test file, test name, mock, assertion, or static test-code reference with a hypothesis or finding only when the supplied discovery handoff explicitly maps that evidence reference to that exact claim. Do not infer a relationship from names, proximity, shared classes, modules, flows, or general subject matter, and omit unrelated test prose rather than using it to enrich an executive summary, finding, or other assessment section.
- When the handoff explicitly maps static test source to the exact claim, say only that the inspected test code contains or asserts the supplied behavior. Static test source alone is not behavioral corroboration, confirmation, validation, characterization, proof that behavior is locked in, or proof that tests executed or passed.
- Use `corroborates`, `confirms`, `validates`, `locks in`, `characterizes current behavior`, or equivalent stronger test language only when the handoff supplies both an exact mapping from the test evidence to the same claim and a deterministic recorded test result. Otherwise preserve the claim using its non-test evidence and state test execution status as `Unknown` when applicable.
- Never claim that this assessment executed or validated tests, runtime behavior, database state, logs, configuration values, external systems, or operational procedures. A result explicitly recorded by the supplied handoff may be reported only as supplied evidence, with its source and limitations preserved.
- Include only evidence-backed risks or explicit unknowns. Do not add content to fill a section; use `None identified from the supplied handoff` where appropriate.
- Do not quote source code or reproduce secrets. Prefer evidence references. Consolidate duplicate evidence only when every original reference remains traceable.
- The assessment may preserve supplied evidence gaps and require a human scope decision, but in `Questions Before Scope Confirmation`, `Prioritized Roadmap`, `Progressive Remediation`, and `Recommendation` it must not use an imperative or interrogative that names a future operational action or directs a human to authorize one. This includes `Does ... need to resolve`, `Authorize bringing`, `resolve`, `confirm`, `reproduce`, `inspect`, `check`, `query`, `run`, `test`, `validate`, or equivalent wording. For unresolved evidence, use only this decision-level pattern: `Human scope decision required on whether the unresolved <evidence gap> should be brought into a future authorized scope.`
- A `Prioritized Roadmap` row may state only: `Human scope decision on whether <evidence gap or decision-level concern> should be brought into a future authorized scope.` It must not begin with `Authorize`, `Confirm`, `Resolve`, or another action verb.
- This prohibition applies to every section, especially `Progressive Remediation`, `Testing Strategy`, `Questions Before Scope Confirmation`, `Prioritized Roadmap`, and `Recommendation`.
- The final `Recommendation` must not restate a specific future operational action.
- When describing credential logging or any secret-related evidence, preserve the handoff's exact demonstrated value origin. Do not call request-supplied values `decrypted` unless the supplied handoff explicitly establishes that the logged values came from decryption.
- Progressive remediation may contain only conditional, decision-level directions, ordered from evidence confirmation through stabilization, resilience, and optional modernization. Evidence-confirmation phases may identify the supplied gap and the required human authorization decision, but must not identify an operational action to perform.
- For an approved `INTERNAL_MODERNIZATION` handoff, state exactly one modernization posture: `Not justified within the approved scope`, `Incremental internal modernization is justified`, or `Unknown; evidence is insufficient`.
- Use `Incremental internal modernization is justified` only when at least one supplied `Technical debt` finding with exact local evidence is relevant to the approved modernization scope and dimensions. Use `Not justified within the approved scope` only when the supplied findings and bounded evidence support that decision across the approved modernization scope and dimensions, with no material relevant gap. Otherwise use `Unknown; evidence is insufficient`.
- Any modernization direction must be conditional, architectural, decision-level, and traceable to supplied findings. Do not provide class-level designs, implementation steps, tests to execute, claim that a whole module requires or does not require modernization, or authorize a refactor.
- Do not recommend or initiate another Skill or model route. Stop after the assessment and require a human scope decision.

### Finding output mapping

- The classification of every supplied discovery `Finding` is authoritative. Preserve it exactly in the assessment.
- A verified `Evidence` item may explain a finding but must never upgrade that finding from `Potential`, `Technical debt`, or `Unknown` to `Verified`.
- A discovery `Evidence` item is not an assessment `Finding`. Do not create a new assessment finding solely from a supplied `Evidence` item or `Hypothesis` when the discovery handoff did not present it as a `Finding`.
- Evidence-only local facts may be summarized only in `Evidence and Limitations`, `Current Architecture`, `Execution Flow`, or `Security and Operational Risks`, with their material uncertainty preserved. Do not place them in Section 7, `Verified Findings`; Section 8, `Potential Findings to Validate`; or Section 10, `Technical Debt`, and do not convert them into a finding, title, severity label, classification, or finding explanation.
- Represent every material supplied discovery `Finding` exactly once in the area selected by its supplied classification:
  - `Verified` → Section 7, `Verified Findings`.
  - `Potential` or `Unknown` → Section 8, `Potential Findings to Validate`.
  - `Technical debt` → Section 10, `Technical Debt`.
- Preserve the supplied evidence references for every represented finding.
- Associate test evidence with a represented finding only when the handoff explicitly maps that evidence reference to that exact finding; related but unmapped test evidence must be omitted and must not support, corroborate, confirm, characterize, lock in, or validate it.
- When the supplied handoff has no `Findings` of a classification mapped to Section 7, 8, or 10, the corresponding assessment section must contain exactly `None identified from the supplied handoff.` It must not contain evidence bullets, titles, severity labels, classifications, or explanations that effectively create a finding.
- A source finding phrased as `Verified code fact; runtime impact Potential` remains `Verified` only for the local code fact. Preserve its impact or reachability as `Potential` or `Unknown`.
- Use exactly one classification label per assessment finding: `Verified`, `Potential`, `Technical debt`, or `Unknown`. Never output combined labels such as `Potential / Unknown`.
- A hypothesis status (`Supported`, `Contradicted`, or `Inconclusive`) is not a finding classification and must not be silently converted into one. `Supported` does not establish root cause.
- Do not normalize or repair discovery-handoff finding classifications; their human review remains an input responsibility outside this Skill.

## 3. Output

Produce the following base structure exactly. Do not add top-level report headings. Keep it compact and evidence-led. Omit empty placeholder prose, but retain every numbered heading and its named subsections. `Internal Modernization Posture` is not part of the base structure: include it only for an approved `INTERNAL_MODERNIZATION` handoff and omit it otherwise.

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

For an approved `INTERNAL_MODERNIZATION` handoff only, append this subsection
under `## 16. Recommendation`:

```markdown
### Internal Modernization Posture

**Posture:** <exactly one permitted posture>
```

Apply these section rules:

- `Status` must be `Draft`; the supplied `magento-discover` handoff is static discovery, not validation.
- `Evidence and Limitations` must identify the supplied handoff, its inspected scope, and every material gap relevant to the objective.
- `Verified Findings`, `Potential Findings to Validate`, and `Technical Debt` may contain only supplied discovery `Findings` mapped by their authoritative classification. When no supplied finding maps to one of these sections, write exactly `None identified from the supplied handoff.` in that section and do not add evidence-only content or finding-like prose.
- `Security and Operational Risks` must contain only applicable evidence-backed risks or explicit unknowns. Otherwise write `None identified from the supplied handoff`.
- `Technical Debt` must preserve that classification and must not present maintainability or compatibility concerns as verified defects.
- `Progressive Remediation` must remain conditional and decision-level. It may preserve supplied evidence gaps and require a human scope decision before additional confirmation, but must not prescribe or recommend an operational action, even conditionally. Use `None identified from the supplied handoff` for an inapplicable phase.
- `Testing Strategy` must state that this assessment performed no tests or runtime validation. Include test evidence only when the handoff explicitly maps its reference to the exact claim being discussed. Mapped static test source establishes only that the inspected code contains or asserts the supplied behavior, not behavioral corroboration, confirmation, validation, characterization, locked-in behavior, execution, or passing status. Stronger test language requires both that exact mapping and a deterministic recorded result; otherwise test execution status is `Unknown` when applicable. Report a qualifying result only as supplied evidence and never as work performed by this assessment. Do not prescribe tests to execute or any other operational validation action.
- `Questions Before Scope Confirmation`, `Prioritized Roadmap`, and `Recommendation` may frame evidence gaps only with the applicable required decision-level pattern and must not ask for or recommend reproduction, test execution, database inspection, log checks, infrastructure queries, or another operational action.
- `Prioritized Roadmap` must not invent priorities. Use only supported priorities or `Unknown`, and keep actions at decision level.
- `Recommendation` must end with a human scope-decision checkpoint. It must not claim authorization, implementation, validation, or a model-routing change.
- `Internal Modernization Posture` must be omitted for standard handoffs. For an approved `INTERNAL_MODERNIZATION` handoff, it must use exactly one permitted posture and may include only conditional architectural directions supported by supplied findings.
- The response must end exactly at the final required assessment content: `## 16. Recommendation`, including `### Internal Modernization Posture` only when applicable. Do not append a conversational epilogue, offer, question, call to action, artifact-saving suggestion, publication suggestion, or a statement such as `let me know`, `I can save`, `I did not write`, `publish`, or equivalent.
- Do not offer to write, save, export, publish, share, or create an artifact. For a standard handoff, the final required content ends with the human scope-decision checkpoint in `## 16. Recommendation`. For an approved `INTERNAL_MODERNIZATION` handoff, `### Internal Modernization Posture` is the final required subsection and must preserve the human scope-decision boundary without adding an offer, instruction, or content after it.

Before responding, silently confirm that:

- the input gate passed;
- an assessment objective explicitly requesting `INTERNAL_MODERNIZATION`, an `Internal Modernization Posture`, or a modernization decision is paired with an explicit, complete, human-reviewed `INTERNAL_MODERNIZATION` discovery handoff, while a standard objective is not blocked merely because its handoff is `STANDARD`;
- every claim is traceable to `$ARGUMENTS`;
- every assessment finding maps back to exactly one supplied discovery `Finding`;
- each assessment finding's classification exactly matches its supplied discovery `Finding` classification;
- every material supplied discovery `Finding` appears exactly once in the required classification area;
- every represented finding preserves its supplied evidence references;
- no `Evidence`-only item or `Hypothesis` became a new assessment finding or populated Section 7, 8, or 10, and every such section with no mapped supplied `Finding` contains exactly `None identified from the supplied handoff.`;
- no combined finding classification labels remain;
- unsupported severity is `Unknown`;
- every test-evidence association has an explicit handoff mapping to the exact hypothesis or finding, and no related but unmapped test evidence is used to enrich or support a claim;
- static test source alone is described only as inspected test code that contains or asserts the supplied behavior, never as behavioral corroboration, confirmation, validation, characterization, locked-in behavior, execution, or a passing result;
- stronger test language is used only when the handoff supplies both the exact claim mapping and a deterministic recorded test result; otherwise the claim relies on its non-test evidence and test execution status is `Unknown` when applicable;
- the assessment does not claim to have executed or validated tests, runtime behavior, database state, logs, configuration values, external systems, or operational procedures;
- no section prescribes, directs, or recommends an operational action, even conditionally;
- `Questions Before Scope Confirmation` uses only the required decision-level pattern for unresolved evidence and contains no imperative, interrogative, or direction to authorize an operational activity;
- every `Prioritized Roadmap` row uses only the permitted roadmap pattern and does not begin with `Authorize`, `Confirm`, `Resolve`, or another action verb;
- `Progressive Remediation` uses only the required decision-level pattern for unresolved evidence and contains no imperative, interrogative, or direction to authorize an operational activity;
- `Recommendation` uses only the required decision-level pattern for unresolved evidence, contains no imperative, interrogative, or direction to authorize an operational activity, and does not restate a specific future operational action;
- credential logging and secret-related evidence preserve the handoff's exact demonstrated value origin, and no request-supplied value is called `decrypted` unless the handoff explicitly establishes decryption as its origin;
- any modernization posture and direction conform to the approved intent, scope, dimensions, and supplied findings;
- no material uncertainty or gap was removed and no missing fact was invented; and
- for a standard handoff, the response ends with the human scope-decision checkpoint in `## 16. Recommendation`; for an approved `INTERNAL_MODERNIZATION` handoff, `### Internal Modernization Posture` is the final required subsection and preserves the human scope-decision boundary without content after it;
- no conversational epilogue, offer, question, call to action, artifact-saving or publication suggestion, or equivalent statement follows the required assessment content; and
- the response does not offer to write, save, export, publish, share, or create an artifact.
