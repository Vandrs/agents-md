---
description: "Remove AI slop from files changed in the current session: unsolicited code comments, redundant documentation, and unnecessary verbosity added without explicit request."
argument-hint: "[file...]"
allowed-tools: Bash(git diff:*)
---

Review and clean up AI-generated noise ("slop") introduced during this session.

## Scope

!`git diff --name-only HEAD 2>/dev/null || git diff --name-only`

$ARGUMENTS

If `$ARGUMENTS` is provided, restrict the cleanup to those files only and ignore the git diff output above.
If `$ARGUMENTS` is empty, use the file list from the git diff output above as the scope.

## Rules

### 1. Code and configuration comments

A comment is considered AI slop if it was **not explicitly requested** by the user or the delegating agent and it merely restates what the code already expresses.

- **Single-line comments** (`//`, `#`, `--`, etc.) that describe obvious operations (e.g., `// initialize the client`, `# set the value`, `<!-- render the button -->`): **remove them silently**.
- **Multi-line or block comments on complex logic** (e.g., algorithm explanations, non-obvious business rules, security notes): **do not remove**. Instead, list each one for the user to review, including the file path and line numbers, and ask whether to keep or remove each one.
- **JSDoc / docstrings / API documentation blocks** that document public interfaces: **do not touch** unless they were explicitly flagged as slop by the user.

### 2. Documentation deduplication

When the same information appears in both a general document (e.g., `README.md`, `AGENTS.md`) and a more specific document (e.g., an agent spec, a module-level doc):

- **Keep** the full content in the **more specific** file.
- **Replace** the duplicate passage in the general file with a concise cross-reference link pointing to the specific file. Example:

  > See [senior-software-engineer.md](./claude/agents/senior-software-engineer.md) for full details.

- Do **not** remove unique context that exists only in the general file.

## Output

For each file modified:
1. Show which comments were removed (file path + original text).
2. Show which doc passages were replaced with links (file path + original passage summary + new link).
3. List any multi-line comments held for user review with file path, line range, and comment content.

After processing all files, present the held-for-review comments as a numbered list and ask the user which ones (if any) should also be removed.
