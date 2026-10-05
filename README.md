# Better Agent

An operational skill for AI coding agents that enforces verified engineering practices, hierarchical codebase inspection, and minimal intervention.

Works with Antigravity, Claude Code, Cursor, Gemini CLI, Codex, and OpenHands.

## Core principles

- Ground Truth Verification: Verify APIs against local package files, exported types, and official documentation. Training memory is often outdated.
- Hierarchical Inspection: Read directory layouts and type interfaces before loading implementation files.
- Symbol Resolution: Use Language Server symbol lookups and reference lists to navigate code and locate callers when available. Do not rely solely on text pattern searches.
- Test Sanctity: Never weaken assertions or remove tests to make failing checks pass. Fix application code to satisfy the test contract.
- Fail-to-Pass Verification: Reproduce defects with a failing test before writing code. Confirm the fix passes.
- Minimal Intervention: Solve problems using the lowest complexity tier: reuse existing code, use standard library APIs, prefer platform primitives, and avoid speculative abstractions.
- Surgical Diffs: Modify only the lines required for the task. Preserve surrounding comments, types, and formatting.
- Persistent References: Maintain verified documentation and API notes in a local references directory.
- Progressive Skill Loading: Load domain-specific instructions only when working in that domain.
- Verification Gate: Run compilers, linters, and tests to confirm changes before completion. Revert uncommitted edits if fixes fail twice.
- Lean Execution: Maintain explicit task lists to track multi-step progress and prevent context drift.
- Workspace Isolation: For risky migrations or speculative refactors, work in an isolated git worktree or branch to keep the primary working tree clean.

## Installation

### Antigravity

Clone into the project skill directory:

```bash
git clone https://github.com/sohamxz/better-agent.git .agents/skills/better-agent
```

For global access:

```bash
# Windows
git clone https://github.com/sohamxz/better-agent.git %USERPROFILE%\.gemini\antigravity\skills\better-agent

# Linux and macOS
git clone https://github.com/sohamxz/better-agent.git ~/.gemini/antigravity/skills/better-agent
```

### Claude Code

Clone into the skills directory:

```bash
git clone https://github.com/sohamxz/better-agent.git ~/.claude/skills/better-agent
```

Or add to CLAUDE.md:

```markdown
Follow the guidelines in .agents/skills/better-agent/SKILL.md
```

### Cursor

Copy SKILL.md into the rules directory:

```bash
mkdir -p .cursor/rules
curl -o .cursor/rules/better-agent.mdc https://raw.githubusercontent.com/sohamxz/better-agent/main/SKILL.md
```

### General, OpenHands, and Hermes

Clone into your runtime skill directory:

```bash
git clone https://github.com/sohamxz/better-agent.git .agents/skills/better-agent
```

## Usage

Reference the skill in your prompt:

```text
Follow the better-agent skill to implement this task.
```

The contents of SKILL.md can also be added directly to your system prompt or custom instructions.

## License

MIT
