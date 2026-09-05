# Workflows

**Version:** `0.1.0`  
**Status:** v0.1 Skills implemented; end-to-end and tool-boundary pilots pending; remaining workflow capabilities planned

## 1. General rules

Every workflow must:

1. begin with an explicit or inferred intake mode;
2. define scope and non-goals;
3. distinguish known facts from hypotheses;
4. read only the minimum relevant code;
5. preserve durable decisions before `/clear`;
6. use deterministic validation where available;
7. stop at human-controlled boundaries;
8. leave Git to the user.

## 2. Common intake

Start from `templates/request.md`.

The minimum useful intake is:

```text
MODE
TASK
SCOPE
CONSTRAINTS
EXPECTED DELIVERABLE
NON-GOALS
```

Business context, symptoms, known facts, and hypotheses should be added when they materially change the work.

`magento-start` normalizes this information and recommends a single next stage, model, and effort level. It does not run the complete workflow automatically or claim that its recommendation changed the runtime.

Before approving a next stage other than `Human scope decision`, the user verifies the active route and changes it only when it differs from the recommendation. `Human scope decision` requests no model-routing change. This keeps routing visible without requiring a redundant selection at every boundary.

Do not define `model` or `effortLevel` in global settings. Use three routing layers instead:

1. Skill frontmatter declares the preferred route for that Skill.
2. Session flags provide deterministic routing for a main-context stage.
3. Agent frontmatter provides isolated routing when a separate context is justified.

For example:

```bash
claude --model sonnet --effort medium
claude --model sonnet --effort high
claude --model haiku --effort low
claude --model opus --effort high
```

These are stage-specific session choices, not global defaults. Explicit session selection, environment variables, organization policy, or runtime behavior may take precedence over Skill frontmatter, so routing remains a human-controlled gate.

## 3. Diagnostic workflow

Use when implementation scope cannot yet be responsibly defined.

```text
Fresh or bounded context
        ↓
Normalized DIAGNOSTIC request
        ↓
Read-only discovery
        ↓
Execution-flow reconstruction
        ↓
Verified findings vs. hypotheses
        ↓
Risk and technical-debt classification
        ↓
Client questions and validation plan
        ↓
Progressive remediation options
        ↓
Human scope decision
```

Required output properties:

- concise executive summary;
- business purpose;
- current architecture and flow;
- evidence with exact files and symbols;
- verified findings clearly labeled;
- potential risks clearly labeled;
- defects separated from technical debt;
- no source-code dump;
- no implementation;
- one recommended next stage.

Use `templates/diagnostic-assessment.md` for the result.

The target `v0.1.0` diagnostic sequence is:

```text
/magento-start <task>
        ↓
review normalized intake and model route
        ↓
preserve the confirmed request
        ↓
/clear or start a routed session when detailed intake context is no longer needed
        ↓
verify the recommended model and effort
        ↓
/magento-discover <confirmed request and scope>
        ↓
review evidence and choose the next stage
```

`magento-discover` is implemented for the v0.1 pilot. Its contract is to use an explicitly verified route, perform bounded read-only inspection, and stop without implementing its recommendations.

Its evidence contract distinguishes inspected local code facts from uninspected application behavior. `Verified` claims cite an exact repository path, line range, and relevant symbol or configuration key; framework, runtime, browser, external-service, database, configuration-value, or uninspected-dependency behavior remains `Potential` or `Unknown`. A verified local code-path gap does not by itself verify runtime impact, severity, reachability, or user-visible effect. Discovery reports must account consistently for inspected and uninspected scope, and must list material unresolved evidence rather than treating runtime or framework uncertainty as absent.

When the approved scope directly contains integration signals, `magento-discover` conditionally derives only the applicable evidence questions for the named entry point, boundary validation, durable state, work progression, Magento mutation, concurrency and failure handling, or observability. It starts at the named anchor and follows only direct execution dependencies needed to answer those questions; the signal does not authorize a repository-wide checklist, scope expansion, runtime access, or external-system inspection. Inspected selectors, counters, state transitions, writes, logs, and configuration references may verify narrower local code facts, while queue blockage, secret disclosure, lost work, duplicate updates, runtime races, business impact, and uninspected MSI behavior remain `Potential` or `Unknown`. Credential or secret values are never reported.

When discovery recommends `Human scope decision`, its model and effort are both `No change` and its routing gate states that no model-routing change is requested. Other next stages retain the human route-verification gate.

### 3.1 Skill tool-enforcement boundary

The pilot requires Claude Code `2.1.236`, the version against which the canonical built-in tool names and Skill-local `disallowed-tools` behavior were reviewed. This is a pilot prerequisite, not a general minimum-version guarantee. Run the pilot without separately configured MCP servers or connectors: their direct tool names are installation-specific, and this toolkit neither configures them nor uses wildcard deny rules.

`magento-start` explicitly denies every reviewed built-in tool that can inspect repository content, execute commands, mutate state, delegate work, invoke another Skill, enter or exit a worktree, or reach network, external, or MCP resources. `magento-discover` uses the same named-denial approach while retaining only `Read`, `Glob`, and `Grep` for direct repository inspection. Claude Code reserves `EndConversation` while any other tool remains, so it is not treated as a workflow capability or listed as an enforceable denial.

Named tool denials are deterministic only during the turn that invokes the Skill. They do not semantically validate intake. Validation before reads, scope discipline, evidence quality, and stop conditions remain prompt- and human-enforced. A later user message clears the Skill-local restrictions. Unsupported or future Claude Code tools require a supported-version review before the pilot version changes.

Pilot checklist:

- Confirm that no MCP servers or connectors are configured for the session; direct installation-specific MCP tool names are not covered by the named Skill-level denials.
- On Claude Code `2.1.236`, invoke `/magento-start` and verify its named denied tools are unavailable.
- Invoke `/magento-discover` with approved input and verify that only `Read`, `Glob`, and `Grep` are available for its repository work.
- Invoke `/magento-discover` with invalid input and record a blocked response without reads as a behavioral result, not as a deterministic no-read guarantee.
- Send a later user message and verify that the Skill-local restrictions have cleared and the session's baseline tool availability has resumed.

## 4. Bugfix workflow

```text
Expected behavior
       +
Observed behavior
        ↓
Reproduce the defect
        ↓
Identify the cause
        ↓
Create a failing regression test
        ↓
Implement the smallest correction
        ↓
Run focused validation
        ↓
Run proportionate broader validation
        ↓
Review scope and regressions
        ↓
Human Git workflow
```

Rules:

- do not combine unrelated modernization with the fix;
- do not rewrite the affected area unless the fix cannot be made safely otherwise;
- confirm that the test failed for the intended reason;
- report validation that was not possible;
- preserve external contracts unless the request explicitly changes them.

## 5. Feature workflow

```text
Business outcome
        ↓
Acceptance criteria
        ↓
Dependency and compatibility discovery
        ↓
Lightweight specification
        ↓
Implementation plan
        ↓
Tests for the next behavior slice
        ↓
Small implementation slice
        ↓
Validation
        ↓
Repeat as needed
        ↓
Review and handoff
```

The specification should state:

- functional behavior;
- interfaces and data contracts;
- failure behavior;
- authorization or security expectations;
- compatibility requirements;
- observability requirements;
- non-goals.

## 6. Refactor workflow

```text
Define the structural problem
        ↓
Identify observable behavior
        ↓
Add or confirm characterization tests
        ↓
Choose one refactor boundary
        ↓
Apply a small structural change
        ↓
Run the same behavior tests
        ↓
Review for accidental behavior changes
        ↓
Repeat only while scope remains controlled
```

Rules:

- a refactor does not intentionally change business behavior;
- suspicious existing behavior remains documented, not silently corrected;
- behavior changes become separate bugfix or feature work;
- broad modernization proposals do not justify broad edits.

## 7. Mechanical workflow

Use for transformations with explicit rules and objective validation.

```text
Exact scope
   ↓
Transformation rule
   ↓
Small-model execution
   ↓
Formatter / linter / focused tests
   ↓
Compact summary
```

Examples:

- add or normalize PHPDoc;
- apply a known coding-standard fix;
- rename a symbol within an authorized boundary;
- update a repetitive declaration;
- classify deterministic validation failures.

Escalate to another mode if the task exposes behavioral ambiguity.

## 8. Characterization workflow for legacy code

Before changing a legacy component:

1. identify its external inputs;
2. identify outputs and side effects;
3. find existing tests and operational evidence;
4. capture representative normal behavior;
5. capture boundary and failure behavior;
6. label suspected defects rather than normalizing them;
7. confirm which behavior is contractually required;
8. refactor only behind the resulting safety net.

Characterization tests are particularly valuable for:

- observers and plugins;
- cron jobs;
- imports and integrations;
- queue consumers;
- inventory calculations;
- pricing rules;
- custom checkout behavior;
- legacy controllers;
- filesystem and database state machines.

## 9. Validation workflow

Run the narrowest useful checks first:

```text
syntax / compile-level check
        ↓
focused unit or integration tests
        ↓
static analysis
        ↓
coding standards
        ↓
broader regression checks when risk requires them
```

Tool output should retain failures and actionable context, not thousands of successful lines.

The handoff distinguishes:

- passed checks;
- failed checks;
- skipped checks;
- checks unavailable in the current environment;
- residual risk.

## 10. Review workflow

An independent review is justified when:

- the change is security-sensitive;
- inventory, price, order, payment, or customer behavior is affected;
- the implementation required complex assumptions;
- the change crosses module boundaries;
- legacy behavior is poorly documented;
- a fresh reviewer is likely to detect shared implementation bias.

Review priorities:

1. correctness and regressions;
2. security and data exposure;
3. concurrency and failure behavior;
4. compatibility;
5. Magento framework conventions;
6. maintainability;
7. unnecessary changes.

Review output uses `Critical`, `High`, `Medium`, and `Low`, with exact evidence and a concrete impact.

## 11. Context reset workflow

Use `/clear` after completing a stage only when the next stage no longer needs the detailed conversation.

Before clearing, capture:

- verified facts;
- decisions;
- acceptance criteria;
- relevant files and symbols;
- constraints;
- validation commands;
- unresolved questions;
- the next action.

Suggested boundaries:

```text
intake → discovery
discovery → planning
planning → implementation
implementation → independent review
```

Do not clear merely because a fixed number of turns has elapsed.

If the next stage uses the same model and effort, `/clear` is sufficient after the handoff has been captured. If the route changes, prefer a new session launched with `--model` and `--effort`; this keeps global settings neutral and makes the stage configuration reproducible.

## 12. Handoff contract

Every completed implementation handoff should answer:

- What changed?
- Why was it necessary?
- What behavior is now protected?
- Which checks passed?
- What could not be validated?
- What risks remain?
- What Git actions should the user consider?

The assistant may recommend Git commands but must not execute them.
