# Technical Diagnostic Assessment

**Template version:** `0.1.0`  
**Assessment version:** <!-- e.g. 1.0.0 -->  
**Date:** <!-- YYYY-MM-DD -->  
**Scope:** <!-- Authorized module, component, or flow -->  
**Status:** Draft

## 1. Executive Summary

<!-- Summarize the purpose, most important evidence, risk, and recommended next decision. -->

## 2. Objective and Constraints

### Objective

<!-- State what the assessment was asked to determine. -->

### Constraints

<!-- Examples: read-only, no Git, preserve contract, limited scope. -->

### Non-Goals

<!-- State what was deliberately not analyzed or implemented. -->

## 3. Evidence and Limitations

<!-- Identify the supplied handoff, inspected scope, static test/configuration evidence, supplied deterministic results, missing operational evidence, and assumptions. Static inspection does not establish execution or passing status. Report gaps as supplied limitations; do not include secrets. -->

Use these labels consistently:

- **Verified:** directly supported by inspected evidence.
- **Potential:** plausible but unestablished from the supplied evidence.
- **Technical debt:** a maintainability or modernization concern supported by exact inspected local evidence, not necessarily a defect.
- **Unknown:** information required from another system or stakeholder.

## 4. Business Purpose

<!-- Explain the component's role, inputs, outputs, and business dependencies. -->

## 5. Current Architecture

### Entry Points

### Authentication and Authorization

### Application Services and Domain Logic

### Persistence and State

### Scheduled or Asynchronous Processing

### CLI and Administrative Operations

### External Dependencies

## 6. Execution Flow

```text
<!-- Show a compact end-to-end flow. -->
```

<!-- Describe important state transitions, failure paths, and observable outputs. -->

## 7. Verified Findings

### [Severity] — Finding title

**Classification:** Verified  
**Evidence:** <!-- Exact supplied file, class, method, configuration, or recorded deterministic result -->  
**Impact:** <!-- Functional, security, operational, or data impact -->  
**Recommended direction:** <!-- Direction only; do not silently expand scope -->

## 8. Potential Findings to Validate

### [Severity] — Hypothesis title

**Classification:** Potential  
**Evidence supporting the hypothesis:**  
**Evidence still required:**  
**Potential impact:**

## 9. Security and Operational Risks

Assess where applicable:

- secret and credential handling;
- input validation;
- authentication and authorization;
- logging and data exposure;
- error handling;
- retry and idempotency;
- concurrency and locking;
- queue progression and poison messages;
- observability and alerting;
- archival and retention;
- recovery and reconciliation.

## 10. Technical Debt

<!-- Separate legacy or unsupported patterns from actual functional defects. Explain maintenance or upgrade impact. -->

## 11. Compatibility and Business Constraints

<!-- Document external contracts and behavior that must not change without approval. -->

## 12. Progressive Remediation

### Phase 0 — Confirm Diagnosis

<!-- Preserve supplied evidence gaps and state the human scope decision required before any additional confirmation. Do not prescribe an operational action. -->

### Phase 1 — Stabilization

<!-- Conditional, decision-level stabilization direction supported by supplied findings. -->

### Phase 2 — Security and Resilience

<!-- Conditional, decision-level security or resilience direction supported by supplied findings. -->

### Phase 3 — Internal Modernization

<!-- Conditional, decision-level architectural directions supported by supplied findings. Do not provide class-level designs, implementation steps, or refactor authorization. -->

### Phase 4 — Optional Architecture Evaluation

<!-- Larger changes that require a separate business and technical decision. -->

## 13. Testing Strategy

<!-- State that this assessment performed no tests or runtime validation. Static test code does not establish execution or passing status; unless the supplied handoff explicitly records a deterministic test result, execution status is Unknown. Report supplied evidence only and do not prescribe any operational action. -->

### Characterization Tests

### Regression Tests

### Integration Tests

### Operational Validation

## 14. Questions Before Scope Confirmation

<!-- Include only decision-level questions whose answers change priority, design, risk, or estimate. Frame additional confirmation as requiring a human scope decision; do not request an operational action. -->

## 15. Prioritized Roadmap

| Priority | Decision-level direction | Reason | Dependency |
|---|---|---|---|
| Critical |  |  |  |
| High |  |  |  |
| Medium |  |  |  |
| Later |  |  |  |

## 16. Recommendation

<!-- End at the human scope decision. Keep any remediation conditional and decision-level; do not prescribe confirmation or another operational action, and do not present unverified hypotheses as committed scope. -->

<!-- For an approved INTERNAL_MODERNIZATION handoff only, add this subsection
under Recommendation:

### Internal Modernization Posture

**Posture:** Not justified within the approved scope | Incremental internal
modernization is justified | Unknown; evidence is insufficient

Keep any direction conditional and architectural; do not authorize a refactor.
Do not add this subsection for a STANDARD handoff. -->
