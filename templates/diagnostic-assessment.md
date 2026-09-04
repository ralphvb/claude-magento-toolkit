# Technical Diagnostic Assessment

**Template version:** `0.1.0`  
**Assessment version:** <!-- e.g. 1.0.0 -->  
**Date:** <!-- YYYY-MM-DD -->  
**Scope:** <!-- Authorized module, component, or flow -->  
**Status:** <!-- Draft | Validated | Superseded -->

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

<!-- Identify the reviewed revision, available tests/configuration, missing operational evidence, and assumptions. Do not include secrets. -->

Use these labels consistently:

- **Verified:** directly supported by inspected evidence.
- **Potential:** plausible but requires reproduction or additional evidence.
- **Technical debt:** a maintainability or modernization issue, not necessarily a defect.
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
**Evidence:** <!-- Exact file, class, method, configuration, or reproducible behavior -->  
**Impact:** <!-- Functional, security, operational, or data impact -->  
**Recommended direction:** <!-- Direction only; do not silently expand scope -->

## 8. Potential Findings to Validate

### [Severity] — Hypothesis title

**Classification:** Potential  
**Evidence supporting the hypothesis:**  
**Evidence still required:**  
**Validation method:**  
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

<!-- Reproduction, metrics, dependency tracing, stakeholder confirmation. -->

### Phase 1 — Stabilization

<!-- Minimal functional corrections and regression coverage. -->

### Phase 2 — Security and Resilience

<!-- Validation, secrets, retries, idempotency, observability, recovery. -->

### Phase 3 — Internal Modernization

<!-- Incremental separation of responsibilities and supported framework patterns. -->

### Phase 4 — Optional Architecture Evaluation

<!-- Larger changes that require a separate business and technical decision. -->

## 13. Testing Strategy

### Characterization Tests

### Regression Tests

### Integration Tests

### Operational Validation

## 14. Questions Before Scope Confirmation

<!-- Include only questions whose answers change priority, design, risk, or estimate. -->

## 15. Prioritized Roadmap

| Priority | Action | Reason | Dependency |
|---|---|---|---|
| Critical |  |  |  |
| High |  |  |  |
| Medium |  |  |  |
| Later |  |  |  |

## 16. Recommendation

<!-- State the next decision or bounded implementation phase. Avoid presenting unverified hypotheses as committed scope. -->
