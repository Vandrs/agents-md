# agents-md

A repository for versioning and sharing agents and skills following the
specifications at https://agents.md and https://agentskills.io.

## What this repo is
Collection of agent specifications in Markdown under the `agents/` folder.
Each file documents an agent's purpose, responsibilities, and operational rules
(frontmatter + human-readable guidance).

## Agents included

### Opencode 

- [staff-engineer-orchestrator.md](./opencode/agents/staff-engineer-orchestrator.md) — Staff-level orchestrator: intake, decompose, delegate.
- [senior-software-engineer.md](./opencode/agents/senior-software-engineer.md) — Senior engineer: implement, review, refactor with SOLID/Clean Architecture practices.
- [security-code-auditor.md](./opencode/agents/security-code-auditor.md) — Security auditor: detect secrets and dependency vulnerabilities.
- [architecture-doc-author.md](./opencode/agents/architecture-doc-author.md) — Architecture author: design docs, mermaid diagrams, migration plans.
- [devops-environment-engineer.md](./opencode/agents/devops-environment-engineer.md) — DevOps engineer: Docker/Containerfile, CI/CD, Kubernetes manifests (delegates container specs to security auditor).

#### How to use
- Clone this repositoryx 
- Make a symlink from opencode expected agent directory to the `opencode/agents/` folder in this repo. For example, if your opencode expects agents in `~/.config/opencode/agent`, run:

  ```bash
  ln -s path/to/this-repo/opencode/agents ~/.config/opencode/agent
  ```



## Contributing
1. Create a branch: `git checkout -b feat/add-my-agent`.
2. Add your agent spec as `agents/<name>.md`. Follow the examples already in `agents/` (frontmatter, description, responsibilities, rules).
3. Open a PR with a short description, usage examples, and a QA checklist.

## License
This repository is available under the MIT License.
