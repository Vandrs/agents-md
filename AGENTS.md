# Project Guidelines

## Purpose

This repository contains agent specifications following [agents.md](https://agents.md) and [agentskills.io](https://agentskills.io). Each `.md` file under `opencode/agents/` defines a single agent's persona, responsibilities, tools, and behavioral rules via YAML frontmatter + Markdown body.

The same specs are mirrored under `claude/agents/` for [Claude Code](https://claude.com/claude-code) — same ids/personas/rules, translated frontmatter (see [Conventions for Claude Code Agent Specs](#conventions-for-claude-code-agent-specs)). When adding or updating an agent, keep both mirrors (`opencode/agents/<id>.md` and `claude/agents/<id>.md`) in sync; same for `opencode/commands/` and `claude/commands/`.

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

## Conventions for Commands

- One command per file under `opencode/commands/`, mirrored under `claude/commands/` with the same filename.
- Every file starts with YAML frontmatter containing at minimum: `description`. Claude Code command files may additionally set `argument-hint` and `allowed-tools` (e.g. `allowed-tools: Bash(git diff:*)` to scope shell access to only the commands the body actually runs).
- The prompt body must be written in English and be explicit about scope, rules, and output format.
- Use `$ARGUMENTS` for user-supplied parameters and `!command` shell injection for dynamic context — both work identically in opencode and Claude Code, so command bodies should stay near-identical across the two mirrors.

## Conventions for Agent Specs

- One agent per file under `opencode/agents/`.
- Every file starts with YAML frontmatter (`---` delimiters) containing at minimum: `description`, `mode` (`primary` or `subagent`), and `tools` permissions.
- The `description` field must clearly state **when** to invoke the agent and include usage examples.
- Body sections: persona, primary responsibilities, behavioral rules, output format, quality checklist.
- Sub-agents that produce artifacts requiring security review must include an explicit delegation step to `security-code-auditor`.
- Do not duplicate logic across agents. If two agents share a concern, one delegates to the other.

## Conventions for Claude Code Agent Specs

- One agent per file under `claude/agents/`, filename identical to its `opencode/agents/` counterpart so cross-references (registry tables, delegation mentions) stay valid as `subagent_type` values.
- Every file starts with YAML frontmatter (`---` delimiters) containing at minimum: `name` (matches the filename stem), `description`, and `tools` (a comma-separated allow-list of actual Claude Code tool names, e.g. `Read, Write, Edit, Bash, Grep, Glob, Agent` — not the opencode per-tool boolean map). `model` is optional and, if set, must be a Claude Code alias (`sonnet`, `opus`, `haiku`, `inherit`); opencode's non-Claude `model` overrides (e.g. `github-copilot/...`) are dropped, not translated.
- There is no Claude Code equivalent of opencode's `mode: primary` — every custom agent is invoked the same way (via the Agent tool). Drop the field.
- Body content (persona, responsibilities, rules, output format) should stay equivalent to the opencode source. The only allowed content changes are: (a) frontmatter, and (b) for agents that mandatorily delegate to another agent (`staff-engineer-orchestrator`, `senior-software-engineer`, `devops-environment-engineer`), a graceful-degradation note stating that if the Agent tool isn't available in the current execution context, the agent must return the delegation payload/request clearly labeled instead of assuming delegation happened.
- Give each agent the minimum tool set its responsibilities require (least privilege): read-only/no-delegation agents (`security-code-auditor`, `research-analyst`) must not get `Write`, `Edit`, or `Agent`; agents whose opencode `tools.task` was `false` must not get `Agent`.

## Build and Test

No build step. Validation is manual: review frontmatter YAML syntax, confirm `description` triggers are accurate, and verify cross-agent delegation references match actual filenames in `opencode/agents/` and `claude/agents/` respectively.

## Contributing

See [CONTRIBUTING section in README](./README.md#contributing) for branch naming, PR process, and QA checklist.
