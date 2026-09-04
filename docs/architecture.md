# Architecture

**Version:** `0.1.0`  
**Status:** validated intake foundation; discovery remains planned

## 1. Objective

This architecture defines a reusable, token-conscious development workflow for Magento Open Source and Adobe Commerce with Claude Code.

It is a methodology layer, not a source of client or project knowledge. It must remain safe to publish and portable between personal and work computers.

## 2. Scope

The architecture covers:

- task intake and classification;
- bounded discovery;
- lightweight Spec-Driven Development;
- TDD and legacy characterization;
- model routing;
- context boundaries and `/clear` usage;
- reusable Skills and selective Agents;
- deterministic validation;
- Git restrictions;
- a staged rollout.

It does not include:

- project `CLAUDE.md` files;
- client-specific Rules or configuration;
- proprietary source code or findings;
- a universal Magento architecture prescription;
- autonomous Git operations;
- automatic end-to-end pipelines.

## 3. Core architecture

```text
                         User request
                              │
                              ▼
                       magento-start
                              │
             ┌────────────────┼────────────────┐
             │                │                │
        classify          normalize        constrain
             │                │                │
             └────────────────┼────────────────┘
                              ▼
                     Human workflow gate
                              │
                              ▼
                magento-discover (planned)
                              │
                  bounded, read-only evidence
                              │
                              ▼
               specification / plan if needed
                              │
                              ▼
                TDD or characterization tests
                              │
                              ▼
                       implementation
                              │
                              ▼
                 deterministic validation
                              │
                              ▼
              independent review when justified
                              │
                              ▼
                      Human-owned Git
```

The user remains the workflow controller. Skills expose stages; they do not silently chain every stage.

## 4. Intake classification

### `DIAGNOSTIC`

Use when the problem, cause, or remediation scope is uncertain.

Default constraints:

- read-only;
- no Git;
- no implementation;
- facts separated from hypotheses;
- concise evidence-backed output;
- open questions and validation plan included.

### `BUGFIX`

Use when expected behavior and observed behavior differ.

Required sequence:

```text
reproduce → failing test → smallest correction → regression validation
```

### `FEATURE`

Use for new behavior. Establish business purpose, acceptance criteria, scope, non-goals, compatibility requirements, and tests before broad implementation.

### `REFACTOR`

Use to improve structure without intentionally changing behavior. Protect legacy behavior with characterization tests before modifying it.

### `MECHANICAL`

Use for low-ambiguity transformations with small scope and deterministic validation. This is the primary Haiku path.

## 5. Lightweight SDD

The process uses only as much specification as the risk requires.

A lightweight specification answers:

1. What behavior exists?
2. What must remain unchanged?
3. What must change?
4. How will success be verified?
5. What is outside scope?
6. Which risks or unknowns remain?

Small tasks may answer these questions in the normalized request. Cross-cutting or high-risk work may require a separate specification and plan.

## 6. TDD and characterization

Behavioral changes use TDD where practical:

- bugfix: reproduce, fail, fix, pass;
- feature: acceptance behavior, test, implementation;
- refactor: characterize, refactor, prove unchanged behavior.

Characterization tests describe what legacy code currently does. They do not automatically declare that behavior correct. Suspected defects remain labeled until business intent or a verified contract determines the desired behavior.

## 7. Model architecture

```text
Low ambiguity              Medium complexity              High complexity
      │                            │                             │
      ▼                            ▼                             ▼
    Haiku                        Sonnet                         Opus
      │                            │                             │
mechanical work          everyday development       architecture and
compact validation       tests and debugging        critical legacy analysis
```

Routing rules:

- choose the least expensive model that reliably completes the task;
- increase effort before changing model when the task remains within the model's strengths;
- return from Opus to Sonnet after the architectural decision is established;
- use deterministic tools instead of model reasoning for deterministic facts;
- exclude Fable.

Global settings must not define `model` or `effortLevel`. Routing belongs to the workflow layer:

```text
Skill frontmatter
    preferred route while a Skill is active

Session launch flags
    deterministic route for a bounded main-context stage

Agent frontmatter
    isolated route when a separate context is justified
```

Skill frontmatter declares routing intent but is not treated as proof of runtime state. An explicit session selection, environment variable, organization policy, or runtime behavior may take precedence. Every stage recommendation therefore ends with a human gate: verify the active model and effort, and change them only when they do not match.

Use session-scoped flags when the route must be deterministic without changing global settings:

```bash
claude --model sonnet --effort medium
claude --model sonnet --effort high
claude --model haiku --effort low
claude --model opus --effort high
```

If the active route already matches the recommendation, continue without a redundant change.

## 8. Context architecture

### Public toolkit context

Contains generic workflows, checklists, templates, and routing policy.

### Project context

Project instructions and any project `CLAUDE.md` remain outside this toolkit. The toolkit neither distributes nor assumes them.

### Task context

Task artifacts preserve the minimum durable state needed across session boundaries:

```text
request.md
discovery.md
spec.md
plan.md
assessment.md
```

Only artifacts required by the task should exist. They must remain outside the public toolkit when they contain project information.

### Conversation context

Conversation history is temporary working memory. Use `/clear` when a stage is complete and its durable facts have been captured. If the next stage requires a different route, prefer a new bounded session launched with `--model` and `--effort`.

## 9. Skills architecture

Skills represent bounded, repeatable procedures.

### Validated Skill: `magento-start`

Responsibilities:

- classify the intake mode;
- normalize the request;
- identify only material missing information;
- establish constraints and non-goals;
- recommend one next stage;
- recommend the least expensive reliable model and effort for that stage;
- require a human routing gate;
- use deterministic next-action templates that do not expand the stated scope.

It must not inspect the repository, use tools, import inherited project facts into intake, perform discovery, or begin implementation.

### Planned Skill: `magento-discover`

Responsibilities:

- inspect only authorized scope;
- reconstruct relevant execution flow;
- identify direct dependencies;
- distinguish findings from hypotheses;
- identify testing and validation entry points;
- return a compact discovery artifact.

It must not modify source code. Its route will be verified explicitly before invocation. Its implementation may use the main session or an isolated Explore context, but that choice must be validated against discovery quality and context cost before becoming part of the stable contract.

### Later Skills

- `magento-plan`;
- `magento-tdd`;
- `magento-phpdoc`;
- `magento-validate`;
- `magento-module-review`.

Each Skill is added only after its workflow has repeated enough to expose a stable contract.

## 10. Agents architecture

No custom Agent is included initially.

Use the main session when discovery, planning, implementation, and testing depend on shared context. A separate Agent is justified when isolation itself adds value.

The first expected Agent is a read-only `magento-reviewer`:

- fresh context;
- Sonnet High by default;
- read/search tools only;
- no source modification;
- no Git;
- findings prioritized by severity with concrete evidence.

An always-on architect Agent is not planned. Use Opus in the main session for the occasional high-risk architectural decision.

## 11. Control and security boundaries

### Git

All Git and GitHub CLI operations are user-owned. This must be enforced through permissions rather than conversational instructions alone.

### Auto Memory

Auto Memory is disabled locally for controlled project workflows. This is a project or environment choice, not a global setting distributed by the toolkit. Explicit project `CLAUDE.md` files remain available and are distinct from Auto Memory.

### Hooks

No Hooks are included in `0.1.0`. They may be introduced after a deterministic policy has proved useful and their output cost is understood.

### MCP and external integrations

No integration is included by default. Add one only when it removes repeated work and its schema, permissions, and context cost are justified.

## 12. Version boundary

Version `0.1.0` is complete when:

- documentation and templates are internally consistent;
- the validated `magento-start` intake contract remains stable after installation;
- `magento-discover` is implemented and its read-only contract is validated;
- manual installation has been tested;
- authorized intake and discovery pilots have produced token and quality measurements;
- client information has remained outside the public repository.
