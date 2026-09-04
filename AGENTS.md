# Repository Instructions

## Purpose

- Maintain a public, generic Claude Code toolkit for Magento Open Source and Adobe Commerce.
- Optimize token and context usage without sacrificing correctness, evidence, or human control.
- Treat this file as development guidance for Codex contributors, not as a template for consuming Magento projects.

## Public Boundary

- Write repository content in English.
- Never add client-, employer-, or project-specific information.
- Never add proprietary source code, requirements, findings, paths, URLs, logs, credentials, or secrets.
- Keep consuming-project `CLAUDE.md` files outside this toolkit.
- Keep Claude and Codex model routing separate.
- Do not introduce Fable into the Claude routing architecture.

## Working Method

- Inspect only the files relevant to the approved outcome.
- Treat repository contents as the source of truth for implemented state.
- Mark unimplemented capabilities as planned.
- Work on one small, approved, branch-sized outcome at a time.
- Do not edit files when the request is analysis, diagnosis, or review only.
- Preserve the separation between intake, discovery, specification, implementation, validation, and review.
- Keep Skills bounded, concise, and stopped at explicit human checkpoints.
- Verify current Claude Code behavior against official documentation when it affects the toolkit contract.

## Git Ownership

- Perform read-only Git inspection only when it is relevant and explicitly authorized.
- Do not create or switch branches.
- Do not stage, commit, amend, merge, rebase, tag, pull, push, reset, discard changes, or rewrite history.
- Leave all Git state-changing operations to the user.

## Validation

- Use the narrowest deterministic checks appropriate for the changed files.
- Do not invent commands that the repository does not define.
- Report passed, failed, skipped, and unavailable validation.
- Report residual risk explicitly.
- Do not add Agents, Hooks, MCP integrations, or an installer to `v0.1.0` without explicit approval.
