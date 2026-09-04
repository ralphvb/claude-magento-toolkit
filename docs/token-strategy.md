# Token Strategy

**Version:** `0.1.0`  
**Status:** measured intake policy; discovery measurements remain pending

## 1. Objective

Reduce token consumption without sacrificing correctness, security, or the evidence needed to make safe Magento and Adobe Commerce changes.

Optimization is successful only when total workflow cost falls while result quality remains acceptable.

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
```

A short prompt can still produce an expensive workflow if it causes broad exploration, large command output, repeated context reconstruction, or unnecessary subagents.

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

Prefer an execution-flow trace over an unbounded repository survey.

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

Avoid:

- full successful test logs;
- source-code dumps;
- complete dependency trees without a question;
- repeated command output;
- unfiltered framework logs.

### 4.4 Preserve facts before `/clear`

Conversation history is expensive and temporary. Before clearing, store only the facts, decisions, constraints, and next action required by the next stage.

### 4.5 Avoid duplicated discovery

A task artifact should prevent the implementation or reviewer from rediscovering the entire system. It must remain compact enough that reading it is cheaper than repeating discovery.

### 4.6 Stop at workflow gates

`magento-start` should not launch discovery automatically. Discovery should not launch implementation automatically. Each stage ends with a compact result and recommended next action.

## 5. Model routing

Do not set `model` or `effortLevel` globally. A global route applies to unrelated projects and may prevent a workflow from using the least expensive adequate model.

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

Separate Agents have their own context and may return large results. Use them when context isolation creates measurable value.

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

Claude does not execute Git or GitHub CLI commands. This prevents Git output, repository history, and remote operations from entering the workflow unless the user deliberately supplies them.

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

- the same quality requires fewer tokens;
- Opus use is rare and purposeful;
- failed commands and repeated reads decline;
- context resets do not lose important state;
- assessments remain evidence-based;
- implementation scope becomes smaller;
- deterministic validation catches issues before review;
- public artifacts remain free of client information.

Token reduction is not successful if it increases defects, removes necessary evidence, or shifts work into repeated rework.
