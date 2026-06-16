# universal-ai-project-template

Universal token-efficient project foundation for AI coding agents in VS Code and terminals: Claude Code, Codex, GitHub Copilot, Cursor, Windsurf, Gemini, DeepSeek, Ollama-powered agents, Continue, and other model wrappers that can read project instructions.

The goal is simple: keep agents useful while cutting avoidable tokens from repeated preambles, restatements, speculation, over-explanation, broad file reads, and unnecessary rewrites.

## What This Adapts

This repository is inspired by [`drona23/claude-token-efficient`](https://github.com/drona23/claude-token-efficient), which shows that a small instruction file can reduce verbose Claude outputs in output-heavy workflows. Its key lessons are:

- Instruction files cost input tokens every turn.
- Savings come mainly from shorter, more focused output.
- Short rules beat huge rulebooks.
- The approach is model-agnostic when the agent actually reads project context.
- Benchmarks from that repo are directional, not a universal guarantee.

This template keeps those ideas but removes the Claude-only assumption. Instead of one `CLAUDE.md`, it provides shared rules plus adapter files for common VS Code agents.

## Files

| File | Used by | Purpose |
| --- | --- | --- |
| `AGENTS.md` | Codex, OpenAI-style agents, many terminal agents | Canonical universal rules |
| `CLAUDE.md` | Claude Code | Claude entry point that delegates to `AGENTS.md` |
| `GEMINI.md` | Gemini CLI and Gemini-aware agents | Gemini entry point that delegates to `AGENTS.md` |
| `.github/copilot-instructions.md` | GitHub Copilot Chat | Repo instructions for Copilot |
| `.cursor/rules/token-efficient.mdc` | Cursor project rules | Always-on Cursor rule file |
| `.cursorrules` | Cursor (deprecated) | Legacy fallback — Cursor no longer reads this file |
| `.windsurfrules` | Windsurf | Windsurf rules |
| `.clinerules` | Cline | Cline entry point that delegates to `AGENTS.md` |
| `.roo/rules/token-efficient.md` | Roo Code | Always-on Roo Code rule file |
| `prompts/universal-token-efficient.md` | Any chat agent | Copy-paste prompt for agents without file support |
| `profiles/` | Optional overlays | Extra rules for coding, automation, debugging, and research |

## Core Behavior

The universal rules tell agents to:

- Read relevant files before editing.
- Avoid rereading unchanged files.
- Prefer targeted edits over full rewrites.
- Start with the result.
- Skip praise, preambles, restatements, and closing fluff.
- Avoid speculative APIs, versions, paths, flags, and package names.
- Keep code simple and aligned with local patterns.
- Run the narrowest useful validation.
- Report uncertainty instead of guessing.

## Quick Start

Copy this repository into a project root, or copy only the files your tools read.

For the broadest setup, keep:

```text
AGENTS.md
CLAUDE.md
GEMINI.md
.github/copilot-instructions.md
.cursor/rules/token-efficient.mdc
.windsurfrules
.clinerules
.roo/rules/token-efficient.md
prompts/universal-token-efficient.md
```

`.cursorrules` is deprecated — Cursor no longer reads it. Keep it only for backward compatibility with old Cursor installations.

For one-off chats, paste the prompt from:

```text
prompts/universal-token-efficient.md
```

## VS Code Agent Coverage

### Claude Code

Claude Code reads `CLAUDE.md`. This template keeps that file tiny and points it to `AGENTS.md`.

### Codex

Codex-style coding agents commonly read `AGENTS.md`. The file is the canonical source of truth for this template.

### GitHub Copilot

Copilot Chat can use `.github/copilot-instructions.md` as repository guidance. This file mirrors the compact universal behavior.

### Cursor

Cursor reads `.cursor/rules/*.mdc` with `alwaysApply: true`. The older `.cursorrules` file is deprecated and kept only for installations that have not migrated to the new rules system.

### Windsurf

Windsurf can use `.windsurfrules`. The file points back to the same concise rules.

### Gemini

Gemini-aware tools may read `GEMINI.md`. This template keeps it short and delegates to `AGENTS.md`.

### Cline

Cline reads `.clinerules` from the project root. This template includes a minimal file that delegates to `AGENTS.md`.

### Roo Code

Roo Code reads files from `.roo/rules/`. This template includes `.roo/rules/token-efficient.md` with `alwaysApply` behavior, delegating to `AGENTS.md`.

### DeepSeek, Ollama, Continue, and Local Agents

These tools differ by wrapper. If your extension or CLI does not automatically read project files, paste `prompts/universal-token-efficient.md` into the system or project instructions field.

For Continue or other VS Code wrappers, add the same prompt to the extension's custom instructions area if project-file discovery is not enabled.

## Profiles

Use profiles as optional overlays. Do not load all of them by default.

| Profile | Use when |
| --- | --- |
| `profiles/coding.md` | Development, refactors, and code reviews |
| `profiles/debugging.md` | Bug investigation and root cause analysis |
| `profiles/automation.md` | Pipelines, multi-agent loops, parseable output |
| `profiles/research.md` | Documentation, comparison, external-source analysis |

Recommended pattern:

```text
Follow AGENTS.md.
Also apply profiles/coding.md for this task.
```

## Prompt Caching

Most providers (Claude, GPT-4o, Gemini) cache stable prompt prefixes when the same content appears at the start of repeated requests. `AGENTS.md` is designed to benefit from this: it is short, stable, and loaded first, so its content is cached across sessions and turns. When caching is active, the input cost of `AGENTS.md` drops to near zero after the first request, making the input/output token equation strongly positive even in short sessions.

To preserve caching effectiveness:

- Do not add dynamic content (timestamps, session IDs, file listings) to `AGENTS.md` or adapter files.
- Keep profiles static and reference them by name rather than inlining their content.
- Add project-specific rules to a separate file rather than editing `AGENTS.md` directly.

Adapter files (`.cursorrules`, `.windsurfrules`, etc.) no longer repeat the rule summary. If the agent reads `AGENTS.md`, the summary is redundant input. If it does not, the summary is too short to be useful. Removing it reduces the recurring input cost of every adapter file to a single line.

## Token Economics

This template saves tokens only when it reduces more output than it adds as recurring input.

It tends to help with:

- Repeated coding sessions.
- Long agent loops.
- CI or automation flows.
- Code review and debugging where agents usually over-explain.
- Teams that need consistent behavior across tools.

It may not help with:

- Single short questions.
- Brainstorming where detail and alternatives matter.
- Tasks where the agent does not read project instructions.
- Workflows that require strict machine-parseable output. Use native JSON mode or tool schemas when available.

## Design Principles

1. Keep the canonical rules short.
2. Put shared behavior in `AGENTS.md`.
3. Use tool-specific files only as adapters.
4. Add profiles only for recurring task types.
5. Prefer verification over confident guesses.
6. Let explicit user instructions override the template.

## Customization

Add only rules that prevent real repeated waste in your workflow.

Good additions:

```text
Never inspect generated build folders unless explicitly asked.
When editing database migrations, run the migration validation script.
For API work, verify route names in the router before referencing them.
```

Avoid broad additions:

```text
Always be perfect.
Think deeply about everything.
Write comprehensive explanations.
Check every file before answering.
```

Broad rules usually increase token usage without improving accuracy.

## Validation Checklist

After installing this template, test one small task in each agent you use:

1. Ask it to inspect a file and summarize only changed behavior.
2. Ask it to make a tiny edit.
3. Check whether it read relevant files first.
4. Check whether it avoided a long preamble.
5. Check whether it reported validation clearly.

If an agent ignores the files, paste `prompts/universal-token-efficient.md` into that agent's custom instructions.

## License And Attribution

Created by Adstronaut Lab, the AI agency behind Adstronaut Marketing and projects like Ather.

We hope this project helps developers and teams build more large, successful, and ambitious AI-assisted projects.

This repository is an independent universal adaptation for multi-agent VS Code workflows.

Inspired by [`drona23/claude-token-efficient`](https://github.com/drona23/claude-token-efficient). Review that repository for the original Claude-focused implementation, profiles, and benchmark notes.
