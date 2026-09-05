# Claude Magento Toolkit

Reusable, token-conscious workflows for developing, diagnosing, testing, and reviewing Magento Open Source and Adobe Commerce code with Claude Code.

**Target version:** `0.1.0`  
**Status:** Three manually invoked diagnostic Skills are implemented; end-to-end and tool-boundary pilots remain pending

## Purpose

This toolkit provides a small, auditable methodology for AI-assisted Magento development. It is designed to:

- minimize unnecessary context and token consumption;
- separate verified facts from assumptions;
- select models according to task complexity;
- use lightweight Spec-Driven Development (SDD);
- apply Test-Driven Development (TDD) to behavioral changes;
- protect legacy behavior with characterization tests;
- keep Git under human control;
- preserve strict boundaries between generic methodology and client information.

The toolkit is intentionally incremental. It currently provides three manually invoked Skills: `magento-start` for tool-free intake, `magento-discover` for bounded read-only repository discovery, and `magento-assess` for tool-free synthesis of a human-reviewed discovery handoff into a private draft.

## Design principles

1. **Tokens are an engineering resource.** Optimize persistent context, file reads, tool output, conversation history, model choice, and response size—not prompts alone.
2. **Evidence precedes implementation.** Do not convert hypotheses into facts or fixes into scope before inspecting the relevant code and behavior.
3. **Humans control workflow boundaries.** A command may classify and recommend the next stage, but it must not silently run an entire analysis-to-implementation pipeline.
4. **Behavioral changes require tests.** Use TDD for bugs and features; use characterization tests before refactoring legacy behavior.
5. **Deterministic validation precedes more model reasoning.** Prefer tests, static analysis, coding standards, and formatters for facts they can establish directly.
6. **Git is human-owned.** Claude may work in the authorized working tree and recommend Git commands, but the user owns branches, commits, history, and remotes.
7. **Public means generic.** Never store client code, credentials, requirements, findings, internal URLs, environment paths, or project-specific instructions in this repository.

## Intake modes

Every task begins in one of five modes:

| Mode | Use when | Default outcome |
|---|---|---|
| `DIAGNOSTIC` | The cause or implementation scope is not yet known | Evidence-backed assessment |
| `BUGFIX` | Expected and observed behavior differ | Minimal fix with regression coverage |
| `FEATURE` | New behavior is required | Acceptance criteria, tests, and implementation |
| `REFACTOR` | Structure should improve without changing behavior | Characterization coverage and incremental refactor |
| `MECHANICAL` | The task is repetitive and low ambiguity | Small transformation plus deterministic validation |

## Workflow

```text
Request
   ↓
magento-start
   ↓
Normalized intake + mode
   ↓
Human checkpoint
   ↓
magento-discover
   ↓
Evidence / bounded context
   ↓
Optional magento-assess synthesis
   ↓
Human scope decision
   ↓
Specification and plan when needed
   ↓
TDD or characterization tests
   ↓
Implementation
   ↓
Deterministic validation
   ↓
Independent review when justified
   ↓
Human-controlled Git
```

See [docs/workflows.md](docs/workflows.md) for mode-specific behavior.

## Model routing

| Work type | Recommended model | Typical effort |
|---|---|---|
| Mechanical edits, compact validation, PHPDoc | Haiku | Low |
| Discovery, normal implementation, unit tests | Sonnet | Medium |
| Difficult debugging and independent review | Sonnet | High |
| Cross-cutting architecture and high-risk legacy analysis | Opus | High |

`Fable` is explicitly outside this architecture.

Model routing is a starting policy, not a permanent rule. Adjust it using measured quality, `/usage`, and `/context` results.

Do not pin `model` or `effortLevel` in global settings. Skill and Agent frontmatter declare the preferred route, but the runtime may also be affected by an explicit session selection, environment variables, or organization policy. Therefore, each stage ends with a human routing gate.

When a stage must run with a guaranteed route, launch a bounded session explicitly:

```bash
claude --model sonnet --effort medium
```

Use `--model` and `--effort` at meaningful stage boundaries, not as permanent personal defaults. If the next stage already matches the active route, continue without changing it.

## Context boundaries

The public toolkit contains only reusable methodology.

Project-specific context—including any project `CLAUDE.md`—is outside the toolkit and remains the responsibility of that project. Task-specific assessments, specifications, plans, and findings also remain outside this public repository.

Use `/clear` at meaningful workflow boundaries after durable facts and decisions have been captured in a small task artifact. Do not use conversation history as the only source of important state.

Auto Memory is expected to be disabled for controlled, auditable workflows. The exact local setting belongs to the user's environment and is not distributed by this repository.

## Repository structure

```text
claude-magento-toolkit/
├── README.md
├── LICENSE
├── .gitignore
├── docs/
│   ├── architecture.md
│   ├── workflows.md
│   └── token-strategy.md
├── skills/
│   ├── magento-start/
│   │   └── SKILL.md
│   ├── magento-discover/
│   │   └── SKILL.md
│   └── magento-assess/
│       └── SKILL.md
├── tests/
│   └── magento-start/
│       └── manual-validation.md
└── templates/
    ├── request.md
    └── diagnostic-assessment.md
```

## Installation

Install the Skills manually so the copied files remain easy to inspect:

```bash
mkdir -p ~/.claude/skills
cp -R skills/magento-start ~/.claude/skills/
cp -R skills/magento-discover ~/.claude/skills/
cp -R skills/magento-assess ~/.claude/skills/
```

Review an existing destination before overwriting it. Restart Claude Code if the top-level personal `skills` directory did not exist when the session started.

All three Skills use `disable-model-invocation: true`, so they are manually invoked and unavailable to automatic model invocation. `magento-start` and `magento-assess` are tool-free during their invoking turns; `magento-discover` retains only direct repository-read tools. Named restrictions are turn-local, while input validation and evidence discipline remain prompt- and human-enforced.

## Usage

For a deterministic Sonnet Medium intake without changing global settings, start a bounded session:

```bash
claude --model sonnet --effort medium
```

Then provide a bounded task description:

```text
/magento-start Diagnose why a custom inventory import does not advance between batches. Perform read-only analysis only.
```

Review the normalized intake, routing gate, and proposed next stage. The route is a recommendation; verify the active runtime and change it only when needed.

A confirmed diagnostic can continue in a clean discovery stage:

```text
/clear
/magento-discover <confirmed normalized request and scope>
```

`magento-discover` uses the active or explicitly routed session, performs bounded read-only discovery, and stops after recommending one next stage. Review its handoff before any further action.

Optionally synthesize one human-reviewed `Complete` or `Partial` discovery handoff into a private assessment draft:

```text
/magento-assess ASSESSMENT APPROVED: yes
ASSESSMENT OBJECTIVE: <objective>
DISCOVERY HANDOFF:
<complete human-reviewed magento-discover report>
```

`magento-assess` uses only the supplied arguments, performs no repository inspection, and stops at a human scope decision. Save its private output outside this public toolkit.

## Public repository boundary

Allowed:

- generic methodology;
- reusable Skills and Agents;
- templates and documentation;
- installation guidance;
- token optimization experiments without client data;
- generally applicable Magento practices.

Never allowed:

- client or employer source code;
- names of clients or internal systems;
- proprietary requirements or issue-tracker content;
- credentials, secrets, payloads, logs, or production data;
- internal URLs and environment paths;
- project-specific findings;
- project `CLAUDE.md` files or private configuration.

## Version `0.1.0`

The first rollout is deliberately small:

1. establish the architecture and workflow contracts;
2. define request and diagnostic templates;
3. document the token strategy;
4. implement `magento-start`;
5. implement `magento-discover`;
6. install the Skills manually;
7. run one authorized diagnostic pilot outside this repository;
8. measure context, usage, quality, and unnecessary work;
9. revise the toolkit from evidence.

No custom Agents, Hooks, MCP integrations, or automatic installer are included in `0.1.0`.

## Roadmap

```text
v0.1.0
├── architecture and workflow documentation
├── request and diagnostic templates
├── magento-start
└── magento-discover

v0.2.0
├── magento-plan
├── magento-tdd
└── legacy characterization workflow

v0.3.0
├── magento-phpdoc
├── magento-validate
├── magento-module-review
└── magento-reviewer
```

Each later capability must be justified by observed repetition, a stable contract, and measurable value.
