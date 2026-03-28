# Project Guidelines

## Purpose

This repository contains agent specifications following [agents.md](https://agents.md) and [agentskills.io](https://agentskills.io). Each `.md` file under `opencode/agents/` defines a single agent's persona, responsibilities, tools, and behavioral rules via YAML frontmatter + Markdown body.

## Architecture — Delegation-First

One **orchestrator** receives all requests and delegates to **specialist sub-agents**. The orchestrator never writes code, config, or infrastructure artifacts — it decomposes tasks and routes them.

| Role | Agent file | Delegates to |
|------|-----------|--------------|
| **Orchestrator** | `opencode/agents/staff-engineer-orchestrator.md` | All specialists below |
| Code implementation | `opencode/agents/senior-software-engineer.md` | `security-code-auditor` (mandatory) |
| Security audit | `opencode/agents/security-code-auditor.md` | — |
| Architecture docs | `opencode/agents/architecture-doc-author.md` | — |
| DevOps / CI / Containers | `opencode/agents/devops-environment-engineer.md` | `security-code-auditor` (mandatory) |
| Research | `opencode/agents/research-analyst.md` | — |

Routing rule: delegate to the best-fit specialist. The orchestrator executes a task itself **only** when no specialist covers it **and** the task is limited to coordination artifacts (plans, summaries, dependency graphs).

## Conventions for Agent Specs

- One agent per file under `opencode/agents/`.
- Every file starts with YAML frontmatter (`---` delimiters) containing at minimum: `description`, `mode` (`primary` or `subagent`), and `tools` permissions.
- The `description` field must clearly state **when** to invoke the agent and include usage examples.
- Body sections: persona, primary responsibilities, behavioral rules, output format, quality checklist.
- Sub-agents that produce artifacts requiring security review must include an explicit delegation step to `security-code-auditor`.
- Do not duplicate logic across agents. If two agents share a concern, one delegates to the other.

## Build and Test

No build step. Validation is manual: review frontmatter YAML syntax, confirm `description` triggers are accurate, and verify cross-agent delegation references match actual filenames in `opencode/agents/`.

## Contributing

See [CONTRIBUTING section in README](./README.md#contributing) for branch naming, PR process, and QA checklist.
