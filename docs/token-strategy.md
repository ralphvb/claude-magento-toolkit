# Token Strategy

**Version:** `0.1.0`  
**Status:** measured DIAGNOSTIC non-blocking intake path; remaining intake and discovery measurements are pending

## 1. Objective

Maximum verifiable quality is the primary objective. Achieve the highest practical quality of evidence, reasoning, safety, maintainability, and human decision support while using tokens efficiently.

Token efficiency minimizes total decision cost across context loading, file reads, tool output, model routing, synthesis, human review, and likely rework. It never means reducing necessary evidence, weakening a workflow contract, lowering validation quality, or creating avoidable rework merely to use fewer tokens. Per-response token count and brevity are not optimization objectives when they reduce decision quality.

## 2. Cost model

Approximate workflow cost as:

```text
fixed session context
+ persistent instructions
+ tool definitions
+ conversation history
+ file contents
+ command and test output
+ Skill instructions
+ Agent results
+ model reasoning
+ final output
+ human review
+ likely rework
```

A short prompt or response can still produce an expensive workflow if it causes broad exploration, large command output, repeated context reconstruction, duplicated synthesis, unnecessary Agents, or avoidable rework.

## 3. Baseline evidence

An initial clean Claude Code session and the later training-project pilot observed approximately:

| Component | Initial observation | Intake pilot |
|---|---:|---:|
| System prompt | 0.4% | 3.5k tokens / 0.4% |
| Tools | 2.6% | 16.7k tokens / 1.7% |
| Explicit memory files | none before `/init` | 5.1k tokens / 0.5% |
| Skills | 0.2% | 2.6k tokens / 0.3% |
| Total initially used | 3–4% | 28k / 967k tokens / 3% |
| Free context | approximately 96% | approximately 93.7% |

The same experiment observed that conversational messages increased after initialization and returned to zero after `/clear`.

These values are environment-specific. Their value is methodological: measure real sessions instead of optimizing from intuition alone.

### 3.1 `magento-start` pilot

Repeated runs used the same bounded DIAGNOSTIC request while model, effort, and Skill size were varied:

| Route and version | Output tokens | Cache write | Cost |
|---|---:|---:|---:|
| Explicit Opus, pre-compaction | 887 | 34.9k | `$0.3713` |
| Default Sonnet, pre-compaction | 856 | 35.1k | `$0.2234` |
| Sonnet High, compact Skill | 907 | 37.1k | `$0.2365` |
| Sonnet Medium, compact Skill | 718 | 37.3k | `$0.2345` |

These are local observations, not controlled pricing benchmarks. Cache state, runtime context, and adaptive reasoning varied between runs.

The useful findings are directional:

- Sonnet cost materially less than the observed Opus run for equivalent intake quality.
- Medium effort reduced output versus the observed High run, while total cost changed little because cache writes dominated.
- Reducing `magento-start` from 1,135 to 748 words improved its permanent design quality but did not guarantee a cheaper individual run.
- The `/usage` percentage attributed to a Skill is cumulative local-session telemetry, not a per-run token breakdown.
- Repeating a stable test can cost more than the additional evidence is worth.

## 4. Optimization priorities

### 4.1 Limit scope before reading

Define:

- included module or domain;
- directly relevant dependencies;
- non-goals;
- expected deliverable;
- maximum useful depth.

Prefer the smallest evidence-backed scope that fully answers the approved question, usually an execution-flow trace rather than an unbounded repository survey. Bounded inspection is not insufficient inspection: expand scope or evidence when doing so materially improves decision quality, and record unresolved limits when it cannot.

Treat modernization as an explicit opt-in lens. Absent or `STANDARD` review intent adds no modernization questions or reads; `INTERNAL_MODERNIZATION` limits its additional evidence work to the approved objective, scope, and one to three dimensions.

### 4.2 Keep permanent context small

Permanent context should contain only information useful across most relevant tasks.

Do not permanently load:

- generic Magento tutorials;
- complete module trees;
- temporary findings;
- task history;
- client-specific assessments;
- information easily discovered from the repository.

Project `CLAUDE.md` files are outside this toolkit.

### 4.3 Bound tool output

Prefer:

- error-only or summary output;
- focused test suites;
- exact file and line references;
- structured output;
- counts plus actionable failures.

Bound output without removing evidence, failure context, or uncertainty needed for the approved decision.

Avoid:

- full successful test logs;
- source-code dumps;
- complete dependency trees without a question;
- repeated command output;
- unfiltered framework logs.

### 4.4 Preserve facts before `/clear`

Conversation history is expensive and temporary. Before clearing, preserve the material evidence, facts, uncertainty, decisions, constraints, and next action required by the next stage. A concise handoff removes duplication; it does not omit decision-relevant information.

### 4.5 Avoid duplicated discovery and synthesis

A task artifact should prevent the implementation, assessment, or reviewer from repeating discovery or synthesis. It must remain compact enough that reading it is cheaper than repeating work while retaining every material claim, evidence reference, constraint, and gap.

### 4.6 Stop at workflow gates

`magento-start` should not launch discovery automatically. Discovery should not launch implementation automatically. Each stage ends with a compact result and recommended next action.

## 5. Model routing

Do not set `model` or `effortLevel` globally. A global route applies to unrelated projects and may prevent a workflow from using the least expensive model route demonstrated to be reliable.

Do not always select the cheapest model. Start with the least expensive reliable route and escalate model capability or effort when evidence shows ambiguity, risk, or insufficient quality. De-escalate after that need is resolved.

Use Skill or Agent frontmatter to declare intent. Treat it as preferred routing rather than unverified runtime fact. At a stage boundary, use session-scoped flags when deterministic routing matters:

```bash
claude --model <model> --effort <level>
```

If the active session already matches the recommendation, do not change it. Use `/usage` to verify the model actually used. Effort telemetry may require confirmation from the active runtime indicator.

### Haiku

Use for:

- low-ambiguity transformations;
- compact validation;
- PHPDoc;
- classification;
- short summaries of structured results.

Require deterministic verification whenever the task changes files.

### Sonnet

Use as the default engineering model for:

- bounded discovery;
- normal implementation;
- unit tests;
- ordinary debugging;
- code review.

Use Medium effort normally and High effort for difficult bugs or reviews.

### Opus

Use for:

- cross-cutting architecture;
- ambiguous legacy integrations;
- high-risk design decisions;
- analysis whose mistakes would create substantial downstream cost.

Once the architectural uncertainty is resolved, return implementation to Sonnet where practical.

### Exclusion

Fable is not part of this routing policy.

## 6. Skills policy

A Skill is token-efficient when it:

- loads only when needed;
- describes a narrow workflow;
- sets explicit scope and output requirements;
- avoids automatic multi-stage execution;
- references additional material only when necessary;
- produces a small durable artifact.

Manually invoked workflow Skills should normally use `disable-model-invocation: true`. This keeps their descriptions out of normal context until the user invokes them.

Do not create a Skill after observing a task once. A good candidate is repetitive, stable, bounded, and easy to validate.

## 7. Agents policy

Separate Agents have their own context and may return large results. Use them only when context isolation or independent reasoning creates measurable decision-quality value greater than the added context and synthesis cost.

Good candidate:

- an independent, read-only review after implementation.

Poor candidates in the initial version:

- one Agent per development role;
- an architect Agent that merely wraps Opus;
- multiple Agents repeating the same repository discovery;
- Agents for mechanical tasks that a small Skill can perform inline.

No custom Agents are included in `0.1.0`.

## 8. Auto Memory policy

Disable Auto Memory for controlled project work.

Benefits:

- explicit and auditable knowledge;
- reduced accidental context growth;
- clearer client-data boundaries;
- reproducible clean sessions.

Local configuration is not distributed in the public repository.

## 9. Git policy

Toolkit workflows do not execute Git or GitHub CLI commands. This prevents Git output, repository history, and remote operations from entering the workflow unless the user deliberately supplies them.

This workflow policy is independent of machine configuration. A work profile should deny Git and GitHub CLI commands used by Claude. A personal profile may retain those permissions for non-toolkit workflows such as training. The public toolkit does not distribute either profile, and an available permission does not change the toolkit's stop-before-Git contract.

The user owns:

- status and diff inspection;
- branches;
- staging;
- commits;
- merges and rebases;
- history changes;
- pulls and pushes;
- pull requests.

## 10. Measurement protocol

For each pilot, record:

```text
task mode
model and effort
context before
context after
usage before
usage after
files inspected
commands executed
size of important tool output
number of workflow stages
quality or rework required
```

Measure at meaningful points:

1. fresh session;
2. after intake;
3. after discovery;
4. after implementation;
5. after validation or review.

## 11. Evaluation questions

After a workflow, ask:

- Did the model inspect unrelated files?
- Was the output longer than the next stage needed?
- Did a higher-cost model materially improve the result?
- Did tool output dominate context?
- Was discovery repeated?
- Did `/clear` occur too early or too late?
- Did the task artifact preserve the right facts?
- Could a deterministic tool replace model reasoning?
- Is this workflow now stable enough to become a Skill?

## 12. Success indicators

The strategy is working when:

- maximum verifiable quality is preserved or improved;
- the same quality requires fewer tokens;
- Opus use is rare and purposeful;
- failed commands and repeated reads decline;
- context resets do not lose important state;
- assessments remain evidence-based;
- implementation scope becomes smaller;
- deterministic validation catches issues before review;
- public artifacts remain free of client information.

Token reduction is not successful if it weakens correctness, evidence, safety, maintainability, validation, workflow contracts, or human control, or shifts cost into review and rework. Do not impose token caps, token-count targets, or brevity rules that can override the evidence required for a reliable decision.
