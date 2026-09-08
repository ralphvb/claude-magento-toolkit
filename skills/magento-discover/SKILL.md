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

Normalize an absent `REVIEW INTENT` to `STANDARD`. If supplied, it must be exactly `STANDARD` or `INTERNAL_MODERNIZATION`. `STANDARD` preserves ordinary discovery and must not add modernization evidence questions or inspection.

Before any repository read, an `INTERNAL_MODERNIZATION` intake must also contain these non-empty fields:

- `MODERNIZATION OBJECTIVE`
- `MODERNIZATION SCOPE`
- `MODERNIZATION DIMENSIONS`

`MODERNIZATION DIMENSIONS` must select one to three distinct items from this closed set:

- `Separation of responsibilities and coupling`
- `Magento/PHP patterns and framework boundaries`
- `Configuration and persistence architecture`
- `Asynchronous processing and operational resilience`
- `Testability and characterization coverage`

The modernization scope cannot expand `SCOPE`; it is a narrower boundary for the optional lens.

`SCOPE` must identify at least one concrete module, domain, path, interface, or execution-flow anchor. Optional intake fields are `BUSINESS CONTEXT`, `KNOWN SYMPTOMS`, `KNOWN FACTS`, `HYPOTHESES TO VERIFY`, `ACCEPTANCE CRITERIA`, `VALIDATION`, and `OPEN QUESTIONS`.

If approval is absent, a required field is missing, `MODE` or `REVIEW INTENT` is invalid, `SCOPE` has no concrete anchor, or an `INTERNAL_MODERNIZATION` field or dimension selection is invalid, use only this structure and stop without inspecting anything:

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

## 3. Apply the conditional integration lens

Apply this lens only when the approved scope directly contains one or more integration signals: inbound requests or entry points; scheduled, command, queue, or asynchronous work; persistence, data movement, files, logs, or archival; Commerce data mutation; or authentication, credentials, payload, retry, locking, batching, or error-handling concerns. An integration signal does not authorize a repository-wide checklist or automatic scope expansion.

Derive only the evidence questions applicable to the approved task from these control points:

- entry point and triggering mechanism;
- input or authentication boundary and validation;
- durable state and lifecycle transitions;
- work selection, batch progression, counters, completion, retry, and archival;
- mutation target and direct Magento dependency;
- concurrency, idempotency, ordering, and failure handling;
- logging, observability, and possible secret exposure.

Start from the named anchor and follow only the direct execution dependencies needed to answer those questions. Do not inspect every control point merely because the module is an integration. Do not inspect runtime logs, databases, credentials, external services, or generated files. Do not execute cron, CLI commands, consumers, requests, tests, validation, or reproduction. The absence of a pattern or Magento convention is not proof of application behavior.

An inspected selector, counter increment, state transition, write call, log call, or configuration reference may be `Verified` as a local code fact. Queue blockage, secret disclosure, lost work, duplicate updates, runtime races, and business impact remain `Potential` or `Unknown` unless inspected local code directly proves the narrower code-path claim. Never expose a credential or secret value; report only the relevant identifier, storage/read/write path, and evidence gap. Distinguish custom inventory persistence or mutation from actual MSI behavior unless the relevant MSI implementation is inspected.

When relevant, integrate this analysis into the existing `Architecture and Execution Flow`, `Evidence`, `Findings`, `Constraints and Limitations`, and `Evidence Gaps and Validation Needed` sections. Do not add output headings or recommend more than one next stage.

## 4. Apply the optional Internal Modernization Lens

Activate this lens only when the approved `REVIEW INTENT` is `INTERNAL_MODERNIZATION` and preflight has passed. Do not activate it for `STANDARD` or an absent intent.

Derive the smallest useful modernization evidence questions from `MODERNIZATION OBJECTIVE`. Inspect only `MODERNIZATION SCOPE`, the selected `MODERNIZATION DIMENSIONS`, and direct execution dependencies required by those questions. Use the same shortest-execution-flow, minimal-inspection, evidence, uncertainty, and stop rules as ordinary discovery.

Record a modernization concern as `Technical debt` only when exact inspected local evidence supports the maintainability or compatibility concern. Keep runtime impact, compatibility consequences, broad absence claims, and behavior owned by uninspected dependencies as `Potential` or `Unknown`. Qualify any bounded absence result with the exact search boundary. Never claim that an entire module does or does not require modernization.

Every reported `Technical debt` finding and every modernization-specific observation must be relevant to at least one explicitly selected `MODERNIZATION DIMENSION`; state the applicable selected dimension or dimensions in its `Relevance` or `Impact`. Do not add findings, evidence, debt, risks, or modernization discussion from an unselected modernization dimension merely because it was encountered during bounded discovery. A fact outside the selected dimensions that is necessary to explain an approved execution flow may be described neutrally only in `Architecture and Execution Flow`; do not promote it into a modernization finding, technical-debt item, assessment candidate, or recommended direction.

Integrate applicable results into the existing output sections. Do not add a repository-wide review, class-level design, implementation step, test execution, refactor authorization, or additional next stage.

## 5. Inspect minimally

1. Start with exact paths and identifiers supplied in the scope. Otherwise use narrowly constrained filename or symbol searches.
2. Inspect only Magento wiring required by the evidence questions, such as module configuration, dependency injection, events, plugins, routes, cron, queues, persistence, or API declarations.
3. Trace the shortest relevant execution path through direct dependencies. If evidence requires crossing the named scope, record the reason and keep the expansion minimal.
4. Inspect adjacent tests and configuration only as existing evidence. Do not execute tests or other validation.
5. Prefer small line-numbered excerpts, exact symbols, focused searches, and summarized command output. Do not dump complete files, logs, dependency trees, or broad repository listings.

Do not use Git or GitHub commands, network access, package managers, databases, external services, generated directories, caches, logs, credentials, or secrets.

## 6. Stop conditions

Stop discovery when any of these conditions applies:

- the evidence questions are answered;
- the next useful action belongs to reproduction, specification, implementation, validation, or review;
- further work would materially expand the approved scope;
- required evidence exists only in an external system, runtime environment, database, log, credential, or stakeholder knowledge;
- the next command could modify state or generate artifacts;
- additional reading would add background rather than decision-relevant evidence.

Report incomplete work as `Partial` or `Blocked`; do not cross the boundary to obtain a more complete answer.

## 7. Evidence and uncertainty

Use these rules:

- `Verified`: only a claim directly supported by inspected repository evidence. Cite the exact repository path, line or line range, and relevant symbol or configuration key. A directly inspected template binding, branch condition, configuration assignment, or module declaration can be `Verified` as a local code fact.
- `Potential`: plausible but not established.
- `Technical debt`: a maintainability or compatibility concern supported by exact inspected local evidence, without proof of a functional defect.
- `Unknown`: cannot be resolved within the approved repository scope.
- Use exactly one canonical classification label for every `Evidence` item and every `Finding`: `Verified`, `Potential`, `Technical debt`, or `Unknown`.
- Never emit a combined, parenthetical, compound, or hybrid classification label, including `Verified (Technical debt)`, `Verified (code fact)`, `Technical debt (with a Potential security consequence)`, or `Verified / Potential`.
- Preserve uncertainty in a separate `Impact`, limitation, or uncertainty sentence. A `Finding` classification remains one canonical label.
- A verified local code fact may support a `Technical debt` finding, but the `Evidence` classification and `Finding` classification must each be independently explicit and canonical.
- User-provided statements remain `Provided`, not `Verified`, until repository evidence corroborates them.
- A hypothesis may be `Supported`, `Contradicted`, or `Inconclusive`; `Supported` does not mean a root cause is proven.
- Do not use Magento conventions, JavaScript semantics beyond the inspected code, framework behavior, user statements, or absent results as sufficient evidence for a `Verified` application-behavior claim.
- Behavior owned by an uninspected framework, runtime, external service, database, browser, configuration value, or direct dependency is `Potential` or `Unknown`. State the verified local code fact separately from its unverified downstream consequence.
- Do not assert as `Verified` without inspecting its implementation: a UI-component configuration overriding a JavaScript default; rendered DOM behavior for an undefined text binding; or the runtime value, positivity, type coercion, timing, or reachability of a configuration or subtotal value.
- A code-path gap is `Verified` only when inspected local code proves that gap. Its runtime impact, severity, reachability, and user-visible effect remain `Potential` or `Unknown` unless directly evidenced.
- Preserve conflicting evidence instead of resolving it through assumption.
- Use Magento conventions only to guide a search, never as evidence of this repository's behavior.
- Uninspected framework or dependency behavior must not be described as stable, assumed, or verified.
- An inspected custom class, route declaration, interface implementation, return value, configuration value, or method call establishes only that local code fact.
- Do not use `bypass`, `bypassed`, `protected`, `unprotected`, `accepted`, `rejected`, `authenticated`, or equivalent effect language for an uninspected framework, transport, router, middleware, or security subsystem in any canonical section.
- If inspected custom code implements a framework-related interface or returns a value from a framework-related method, report only the exact local implementation, method, and return value. The resulting framework behavior remains `Unknown` unless the relevant framework implementation is inspected.
- Do not infer or describe an uninspected framework, transport, middleware, router, security subsystem, serializer, logger implementation, or deployment effect from that fact.
- Do not claim an endpoint is transport-authenticated, unauthenticated-by-transport, publicly reachable, CSRF-bypassed at the framework layer, protected, rejected, logged to a destination, encrypted in transit, or otherwise affected by an uninspected dependency.
- When the framework or dependency implementation is outside scope, describe only the local code fact and state the downstream framework or runtime effect as `Unknown`.
- For example, if a class method returns `true` from a CSRF-related interface method, report only that return value and the class and method implementing it; do not call this a framework-layer bypass unless the relevant framework behavior was inspected.
- Findings must describe only the observed maintainability, coupling, persistence, or testability concern and its evidence-backed impact. Do not name a target implementation mechanism, replacement pattern, configuration location, class design, refactor, migration, or solution alternative. In particular, do not frame a finding as `rather than externalizing to <mechanism>` or equivalent. Keep any future modernization direction conditional and decision-level only.
- Never make an unqualified absence claim from a partial code-path inspection. If reporting that a behavior or call is absent, state the exact inspected path, files, symbols, and/or bounded search that supports the absence.
- Do not say files are `never` deleted, renamed, or marked consumed unless the bounded inspection or search covers every relevant in-scope path needed for that claim. Otherwise state the narrower verified fact, such as that no removal or marker operation was found in the specifically inspected processing loop.
- Assign severity or impact only when evidence supports it; otherwise mark it `Unknown`.
- `Missing evidence` must list each material unresolved evidence need. Do not write `None within approved scope` while runtime, framework, configuration, dependency, or other material uncertainty remains.
- Preserve the exact demonstrated origin of every value throughout the complete report, including `Executive Summary`, `Architecture and Execution Flow`, `Evidence`, `Hypotheses`, `Findings`, limitations, and recommendations.
- A request-supplied value remains request-supplied wherever it is described, even when compared against a configuration value obtained through decryption. A configuration value obtained through decryption remains configuration-sourced wherever it is described.
- Do not transform, merge, or relabel value origins across sections. When evidence reveals multiple distinct origins, describe each separately and preserve any uncertainty rather than resolving the distinction by inference.
- Do not state or imply that a logger receives a decrypted configuration value unless the inspected logging call directly receives that decrypted value.
- Static inspection of test files, test names, mocks, assertions, or test code establishes only the existence and inspected contents of that code.
- A static test reference may support only the behavior, inputs, mocks, calls, or assertions explicitly present in the cited test code. Do not infer or state unasserted prior state, cross-invocation behavior, runtime conditions, coverage, or relationships from a single test invocation.
- If a test does not explicitly exercise a prior counter, prior queue state, repeated call, or other stateful condition, do not use it to support a claim about that condition.
- When source implementation already supports a code fact, omit a test reference that does not directly add exact evidence.
- Associate test source with a hypothesis, `Evidence` item, or `Finding` only when the inspected test code directly supports that exact claim.
- An `Evidence` item that includes static test source may be referenced only by a `Hypothesis` or `Finding` whose exact claim is directly supported by the same cited test assertion or construction.
- Do not reuse counter, error-handling, archival, or other test evidence to support a different concern such as batch selection, dispatch, routing, authentication, or persistence merely because it occurs in the same class or flow.
- If source implementation supports a claim and no exact test `Evidence` exists, cite only the source implementation. If a different test is relevant, define a separate `Evidence` item with its exact test path, method, and static assertion before referencing it.
- Every test reference used in a `Hypothesis`, `Finding`, `Executive Summary`, `Evidence`, or another canonical section must either point to an explicitly declared `Evidence` item that maps the cited test to that exact claim, or directly state the exact test path, test method, and specific static assertion or construction it supports.
- Do not reuse a test `Evidence` item to support a different hypothesis, `Finding`, or concern merely because the test file, class, or flow is related.
- Do not cite a test in a `Finding`'s evidence references unless that test evidence is explicitly mapped to that exact `Finding` or directly described there with its exact test method and static claim. If source implementation already supports the claim, omit unrelated test evidence instead of broadening the evidence set.
- Do not describe static test source as corroborating, confirming, validating, proving, characterizing current runtime behavior, pinning behavior, locking behavior in, or showing that a behavior is intended.
- Do not claim tests executed, passed, are passing, or validate application behavior unless a deterministic execution result is recorded in the discovery evidence. In the ordinary read-only static-discovery flow, test execution status is `Unknown`.
- When static test source is relevant, use wording such as: “The inspected test code contains/asserts <specific supplied behavior>.” Preserve the distinction between that assertion and runtime behavior.
- Bound every claim about missing, absent, insufficient, or non-characterized test coverage — including claims in an `Evidence` item's `Relevance`, a `Hypothesis`'s `Missing evidence`, or a `Finding` title or `Impact` — to the exact inspected test files, symbols, and searches. Do not generalize from inspected tests to “existing unit coverage,” “the test suite,” “all tests,” or repository-wide coverage when any relevant test files remain uninspected.
- Do not use `all`, `only`, `every`, `the inspected unit tests`, or equivalent group-wide language for test construction or coverage unless every test in that stated group was inspected and directly supports the claim. Otherwise name only the exact test files, test methods, or selected inspected tests that establish the observed construction pattern.
- A `Finding` title and its `Impact` must use the same bounded scope as the cited test evidence.
- If uninspected tests could affect the claim, state that their coverage is `Unknown` and include them in `Scope not inspected` or `Evidence Gaps and Validation Needed` as appropriate. Prefer wording such as: “The inspected unit tests do not characterize <exact interaction>” rather than a broad claim about the entire suite.

Keep `Scope inspected`, `Files inspected`, `Evidence`, and `Scope not inspected` internally consistent. List each inspected file in `Files inspected`, and cite it in `Evidence` only for claims it supports. If a direct dependency is named but its internals are not inspected, list it in `Scope not inspected` and explain why its internals were not needed to answer the approved static question.

Do not rely on, describe, cite, or infer repository behavior from a file listed under `Scope not inspected`. Such a file may be named as an uninspected dependency only to preserve the boundary, not as evidence supporting an architecture claim or `Finding`. If a claim requires that file, inspect it only when the approved scope and minimal direct-dependency rule justify it; otherwise omit that part of the claim.

Every reference from a `Hypothesis`, `Finding`, `Executive Summary`, or another canonical section to a `Finding` must be traceable to a visible `Finding` title or an explicitly defined identifier in the same report. Do not use invented or implicit identifiers such as `TD1–TD6` unless each identifier is visibly declared on its corresponding `Finding`. Prefer exact `Evidence` references and concise `Finding` titles over additional identifiers.

## 8. Output

Use exactly this structure:

```markdown
## Discovery Status

- Outcome: Complete | Partial | Blocked
- Mode:
- Review intent: STANDARD | INTERNAL_MODERNIZATION
- Modernization objective: <include only for INTERNAL_MODERNIZATION>
- Modernization scope: <include only for INTERNAL_MODERNIZATION>
- Modernization dimensions: <include only for INTERNAL_MODERNIZATION>
- Discovery question:
- Scope inspected:
- Scope not inspected:
- Files inspected:
- Commands executed:

## Executive Summary

## Architecture and Execution Flow

## Evidence

### E1 — <concise claim>

- Classification: Verified | Potential | Technical debt | Unknown
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

Before responding, silently confirm that:

- every cross-reference to a `Finding` resolves to its visible title or a visibly declared identifier;
- every absence claim states its exact bounded inspection or search and does not exceed that boundary;
- no file under `Scope not inspected` supports, informs, or is cited by a repository-behavior claim;
- every `Finding` remains diagnostic, without a target implementation mechanism or solution alternative;
- no framework, transport, or dependency effect is asserted beyond inspected local code;
- no prohibited framework-effect terminology is used for an uninspected framework, transport, router, middleware, or security subsystem, and any resulting framework behavior is `Unknown` unless the relevant implementation was inspected;
- every secret-, credential-, request-, and configuration-value origin is internally consistent across the entire output;
- static test source is described only as inspected code content unless deterministic execution evidence exists;
- every test association maps to the exact claim;
- every test reference maps to its exact claim;
- no test `Evidence` item is reused for a different claim without an explicit exact mapping;
- every test-backed `Evidence` reference in `Hypotheses` and `Findings` is exact and non-reused;
- every static test reference supports only behavior, inputs, mocks, calls, or assertions explicitly present in the cited test code, without inferring unasserted stateful or cross-invocation conditions;
- every redundant test reference that does not add exact evidence beyond source implementation is omitted;
- every group-wide test construction or coverage claim is supported by inspection of every test in the stated group; otherwise the claim names only the exact supporting test files, methods, or selected inspected tests;
- every `Finding` title and `Impact` uses the same bounded scope as its cited test evidence; and
- every test-coverage absence claim, including one in an `Evidence` item's `Relevance`, a `Hypothesis`'s `Missing evidence`, or a `Finding` title or `Impact`, states its exact inspected test-file, symbol, and search boundary and does not generalize over uninspected tests.

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

Whenever the selected stage is `Human scope decision`, `Suggested action` must be exactly `Wait for human scope approval before starting another stage.` Do not append examples, parenthetical text, alternative stages, explanations, or follow-up suggestions to that field. Put any explanation only in `Reason` or another applicable canonical section.

For either `Human scope decision` row, `Model` must be exactly `No change`, `Effort` must be exactly `No change`, and the routing gate must say exactly `No model-routing change is requested.` Do not ask the user to verify a model or effort when both are `No change`.

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
- the normalized review intent is valid, and modernization fields appear only for `INTERNAL_MODERNIZATION`;
- the Internal Modernization Lens ran only for a preflight-approved `INTERNAL_MODERNIZATION` intake;
- every `Evidence` item and every `Finding` uses exactly one canonical classification label, with uncertainty stated separately;
- for `INTERNAL_MODERNIZATION`, every `Technical debt` finding and every modernization-specific observation explicitly maps to one or more selected `MODERNIZATION DIMENSIONS`, and no unselected-dimension content is promoted beyond neutral execution-flow context;
- every submitted or secret-related value origin is described only as established by inspected code, without inferring decryption from a comparison against decrypted configuration;
- exactly one next stage is recommended;
- the Stage is valid for this Skill;
- when the Stage is `Human scope decision`, Model is exactly `No change`, Effort is exactly `No change`, Routing gate is exactly `No model-routing change is requested.`, and Suggested action is exactly `Wait for human scope approval before starting another stage.` with nothing appended;
- for another Stage, the routing gate uses the canonical form;
- no incomplete words, placeholders, or contradictory alternatives remain.
