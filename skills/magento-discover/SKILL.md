---
name: magento-discover
description: Perform bounded, evidence-based, read-only repository discovery for a human-approved Magento Open Source or Adobe Commerce task and recommend one next stage.
argument-hint: "[approved normalized request]"
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
  - ListAgents
  - ListMcpResourcesTool
  - LSP
  - Monitor
  - NotebookEdit
  - PowerShell
  - PushNotification
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

# Magento Discover

Use `$ARGUMENTS` as the approved intake. Respond in English. Preserve identifiers, paths, commands, and product names exactly as provided.

## Discovery boundary

This Skill performs bounded, read-only repository discovery only. Do not reclassify or expand the intake, modify files, use Git or GitHub, reproduce behavior, write specifications or plans, implement, refactor, test, validate, or review. Do not run commands that can change state, generate artifacts or caches, access a database or external service, or expose secrets.

The named `disallowed-tools` entries leave only the direct repository-read tools `Read`, `Glob`, and `Grep` available for this Skill's work, subject to the session's baseline permissions. The denials apply only during the invoking turn. They do not validate the intake before a read or enforce scope, evidence quality, or stop conditions; those controls remain prompt- and human-enforced. A later user message clears the Skill-local tool restrictions.

Do not write task-specific findings into this public toolkit. Keep discovery results in the response or in a separately authorized private artifact.

## 1. Preflight

Before using any tool, require these non-empty fields:

- `APPROVED: yes`
- `MODE`
- `TASK`
- `SCOPE`
- `CONSTRAINTS`
- `EXPECTED DELIVERABLE`
- `NON-GOALS`

Accept fields as labels or headings in a normalized request. `MODE` must be exactly one of `DIAGNOSTIC`, `BUGFIX`, `FEATURE`, `REFACTOR`, or `MECHANICAL`.

`SCOPE` must identify at least one concrete module, domain, path, interface, or execution-flow anchor. Optional intake fields are `BUSINESS CONTEXT`, `KNOWN SYMPTOMS`, `KNOWN FACTS`, `HYPOTHESES TO VERIFY`, `ACCEPTANCE CRITERIA`, `VALIDATION`, and `OPEN QUESTIONS`.

If approval is absent, a required field is missing, `MODE` is invalid, or `SCOPE` has no concrete anchor, use only this structure and stop without inspecting anything:

```markdown
## Discovery Blocked

- Missing or invalid input:
- Suggested action:
```

Identify only the invalid or missing input and the action needed to correct it. Do not infer a likely `MODE` or provide task analysis.

Treat project instructions as operating constraints, not as evidence of application behavior.

## 2. Define evidence questions

Convert the approved task, known symptoms, and hypotheses into the smallest useful set of evidence questions. Do not add business requirements, implementation mechanisms, or Magento conventions to the approved facts or scope.

Use the named scope plus directly connected execution dependencies as the default boundary. Do not follow second-order dependencies unless the approved question cannot otherwise be answered.

## 3. Inspect minimally

1. Start with exact paths and identifiers supplied in the scope. Otherwise use narrowly constrained filename or symbol searches.
2. Inspect only Magento wiring required by the evidence questions, such as module configuration, dependency injection, events, plugins, routes, cron, queues, persistence, or API declarations.
3. Trace the shortest relevant execution path through direct dependencies. If evidence requires crossing the named scope, record the reason and keep the expansion minimal.
4. Inspect adjacent tests and configuration only as existing evidence. Do not execute tests or other validation.
5. Prefer small line-numbered excerpts, exact symbols, focused searches, and summarized command output. Do not dump complete files, logs, dependency trees, or broad repository listings.

Do not use Git or GitHub commands, network access, package managers, databases, external services, generated directories, caches, logs, credentials, or secrets.

## 4. Stop conditions

Stop discovery when any of these conditions applies:

- the evidence questions are answered;
- the next useful action belongs to reproduction, specification, implementation, validation, or review;
- further work would materially expand the approved scope;
- required evidence exists only in an external system, runtime environment, database, log, credential, or stakeholder knowledge;
- the next command could modify state or generate artifacts;
- additional reading would add background rather than decision-relevant evidence.

Report incomplete work as `Partial` or `Blocked`; do not cross the boundary to obtain a more complete answer.

## 5. Evidence and uncertainty

Use these rules:

- `Verified`: only a claim directly supported by inspected repository evidence. Cite the exact repository path, line or line range, and relevant symbol or configuration key. A directly inspected template binding, branch condition, configuration assignment, or module declaration can be `Verified` as a local code fact.
- `Potential`: plausible but not established.
- `Technical debt`: a maintainability or compatibility concern without proof of a functional defect.
- `Unknown`: cannot be resolved within the approved repository scope.
- User-provided statements remain `Provided`, not `Verified`, until repository evidence corroborates them.
- A hypothesis may be `Supported`, `Contradicted`, or `Inconclusive`; `Supported` does not mean a root cause is proven.
- Do not use Magento conventions, JavaScript semantics beyond the inspected code, framework behavior, user statements, or absent results as sufficient evidence for a `Verified` application-behavior claim.
- Behavior owned by an uninspected framework, runtime, external service, database, browser, configuration value, or direct dependency is `Potential` or `Unknown`. State the verified local code fact separately from its unverified downstream consequence.
- Do not assert as `Verified` without inspecting its implementation: a UI-component configuration overriding a JavaScript default; rendered DOM behavior for an undefined text binding; or the runtime value, positivity, type coercion, timing, or reachability of a configuration or subtotal value.
- A code-path gap is `Verified` only when inspected local code proves that gap. Its runtime impact, severity, reachability, and user-visible effect remain `Potential` or `Unknown` unless directly evidenced.
- Preserve conflicting evidence instead of resolving it through assumption.
- Use Magento conventions only to guide a search, never as evidence of this repository's behavior.
- Uninspected framework or dependency behavior must not be described as stable, assumed, or verified.
- Qualify every absence claim with the bounded search that was performed.
- Assign severity or impact only when evidence supports it; otherwise mark it `Unknown`.
- `Missing evidence` must list each material unresolved evidence need. Do not write `None within approved scope` while runtime, framework, configuration, dependency, or other material uncertainty remains.

Keep `Scope inspected`, `Files inspected`, `Evidence`, and `Scope not inspected` internally consistent. List each inspected file in `Files inspected`, and cite it in `Evidence` only for claims it supports. If a direct dependency is named but its internals are not inspected, list it in `Scope not inspected` and explain why its internals were not needed to answer the approved static question.

## 6. Output

Use exactly this structure:

```markdown
## Discovery Status

- Outcome: Complete | Partial | Blocked
- Mode:
- Discovery question:
- Scope inspected:
- Scope not inspected:
- Files inspected:
- Commands executed:

## Executive Summary

## Architecture and Execution Flow

## Evidence

### E1 — <concise claim>

- Classification: Verified
- Evidence: <exact path:line or line range; relevant symbol or configuration key>
- Relevance:

## Hypotheses

### <hypothesis>

- Status: Supported | Contradicted | Inconclusive
- Evidence references:
- Missing evidence:

## Findings

### <finding>

- Classification: Verified | Potential | Technical debt | Unknown
- Impact:
- Evidence references:

## Constraints and Limitations

## Evidence Gaps and Validation Needed

## Recommended Next Stage

- Stage:
- Model:
- Effort:
- Reason:
- Routing gate:
- Suggested action:
```

Use `None` where a section has no applicable content. Prefer evidence references over repeated explanations or source excerpts. Recommend exactly one next stage and stop for human confirmation.

`MODE` and `Stage` are separate concepts. A Stage must never contain a Mode value such as `DIAGNOSTIC` or `BUGFIX`.

Choose the first matching row in this prioritized next-stage decision table without performing the stage:

| Priority | Condition | Stage | Model | Effort | Suggested action |
| --- | --- | --- | --- | --- | --- |
| 1 | Repository evidence resolves the approved static discovery question, but any further action would require new authorization. | `Human scope decision` | `No change` | `No change` | `Wait for human scope approval before starting another stage.` |
| 2 | The next evidence needed would materially expand the approved scope. | `Human scope decision` | `No change` | `No change` | `Wait for human scope approval before starting another stage.` |
| 3 | The approved intake contains an actual observed runtime behavior that cannot be resolved through authorized repository evidence. | `Reproduction` | Apply routing rules below. | Apply routing rules below. | Ask for human approval to reproduce the observed runtime behavior within the approved scope. |
| 4 | Approved new behavior is sufficiently defined for specification. | `Lightweight specification` | Apply routing rules below. | Apply routing rules below. | Ask for human approval to begin lightweight specification within the approved scope. |
| 5 | Approved legacy behavior must be preserved. | `Characterization of current behavior` | Apply routing rules below. | Apply routing rules below. | Ask for human approval to characterize current behavior within the approved scope. |
| 6 | The approved task is a deterministic mechanical change. | `Bounded transformation with deterministic validation` | Apply routing rules below. | Apply routing rules below. | Ask for human approval to perform the bounded transformation and its deterministic validation. |

For priority 1, use exactly:

```text
- Stage: Human scope decision
- Model: No change
- Effort: No change
- Routing gate: No model-routing change is requested.
- Suggested action: Wait for human scope approval before starting another stage.
```

For either `Human scope decision` row, the routing gate must say exactly `No model-routing change is requested.` Do not ask the user to verify a model or effort when both are `No change`.

Recommend `Reproduction` only under priority 3. A static hypothesis that remains unconfirmed at runtime is not, by itself, a reason to recommend `Reproduction`.

Choose the least expensive reliable route for that next stage:

- bounded discovery, normal implementation, or tests → `Sonnet`, `Medium`;
- difficult debugging, high ambiguity, or independent review → `Sonnet`, `High`;
- cross-cutting architecture, critical integrations, or high-risk legacy decisions → `Opus`, `High`;
- mechanical deterministic work → `Haiku`, `Low`.

The next-stage model and effort are recommendations, not confirmed runtime state. Never claim this Skill changed the route for a later turn. For a stage other than `Human scope decision`, use this routing gate, replacing only the placeholders:

```text
Human confirmation required: verify <MODEL> / <EFFORT> before approving the next stage.
```

Stop after the recommendation and wait for human confirmation.

Before responding, silently check output conformance:

- all required headings are present;
- exactly one next stage is recommended;
- the Stage is valid for this Skill;
- the routing gate is `No model-routing change is requested.` for `Human scope decision`, or uses the canonical form for another stage;
- no incomplete words, placeholders, or contradictory alternatives remain.
