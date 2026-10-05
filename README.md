# alfa-reza/skills

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Skills](https://img.shields.io/badge/skills-11-black.svg)](#available-skills)

A collection of engineering skills for AI coding agents, compatible with the [skills CLI](https://github.com/vercel-labs/skills).

## Installation

Install skills using [`npx skills`](https://github.com/vercel-labs/skills):

```bash
npx skills add alfa-reza/skills
```

Install a specific skill, install globally, or target a specific agent:

```bash
# Install a single skill
npx skills add alfa-reza/skills --skill code-audit

# Install globally across projects
npx skills add alfa-reza/skills -g

# Target a specific coding agent (e.g., claude-code, cursor, opencode, codex)
npx skills add alfa-reza/skills --skill commit -a claude-code
```

## Available Skills

| Skill | Description |
| --- | --- |
| [`code-audit`](skills/code-audit/SKILL.md) | Perform evidence-based audits for defects, security, reliability, performance, compatibility, test gaps, architecture, and unnecessary complexity. |
| [`code-review`](skills/code-review/SKILL.md) | Review software changes for defects, regressions, security risks, specification compliance, compatibility, and maintainability. |
| [`commit`](skills/commit/SKILL.md) | Create safe, atomic Git commits following Conventional Commits with staged-diff review. |
| [`debug`](skills/debug/SKILL.md) | Diagnose and resolve software failures through evidence-driven debugging across languages and platforms. |
| [`optimize`](skills/optimize/SKILL.md) | Profile, benchmark, and optimize software performance, latency, throughput, and resource utilization with measurable evidence. |
| [`pr`](skills/pull-request/SKILL.md) | Draft and publish pull requests aligned with repository templates, reviewer-first descriptions, and merge risk checks. |
| [`refactor`](skills/refactor/SKILL.md) | Improve existing code structure without changing observable behavior, applying KISS, YAGNI, and DRY with risk-based verification. |
| [`rtfm`](skills/rtfm/SKILL.md) | Retrieve and verify official technical documentation before making claims or changes that depend on external APIs, libraries, SDKs, or CLIs. |
| [`simplicity`](skills/simplicity/SKILL.md) | Keep code changes minimal and complete (KISS, YAGNI, DRY) before designing, writing, fixing, or reviewing code. |
| [`unslop`](skills/unslop/SKILL.md) | Remove AI writing patterns from prose while preserving meaning, tone, and technical accuracy. |
| [`writing-for-llm`](skills/writing-for-llm/SKILL.md) | Write, review, and optimize instructions for language models, prompts, AGENTS.md, SKILL.md, and subagent delegation. |

## License

[MIT](LICENSE) © 2026 Faturahman Alfareza
