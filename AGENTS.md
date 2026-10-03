# AGENTS.md - Agent Guidelines and Repository Standards

This document establishes operational rules for AI coding agents working in the `alfa-reza/skills` repository. All agents, subagents, and automated tools contributing to or operating in this repository must strictly follow these instructions.

---

## 1. If You Are an AI Agent

Stop. Read this section before proposing, creating, or modifying any skill in this repository.

This repository is a curated collection of production-grade AI agent skills for software engineering. These skills are executed directly by autonomous coding agents across multiple harnesses (Vercel Skills CLI `npx skills`, Claude Code, Cursor, Windsurf, Codex, and others).

Every sentence in a skill shapes runtime agent behavior. A sloppy skill burns context tokens, causes infinite tool loops, induces hallucinations, or breaks production codebases. Your job is to produce dense, battle-tested, high-signal operational runbooks.

---

## 2. Strict Content Rules

### Zero Useless External Links as "Sources" (Strict Rule)

**Never include external documentation links, generic URLs, or bibliographies under the guise of "Sources", "References", or "Further Reading".**

- **Prohibited**:
  - Adding links to official documentation (e.g., Git manual, MDN, language specifications, RFCs, Oracle Java docs).
  - Adding links to external blog posts, articles, or tutorials to "cite" common software engineering concepts (e.g., linking to Martin Fowler, Sandi Metz, or vendor blogs).
  - Adding bibliographical sections at the bottom of skills simply because reference material was consulted during research.
- **Why this is banned**:
  - AI agents executing skills already possess strong pre-trained domain knowledge and have access to local environment tools (`--help`, `man` pages, compilers, linters, tests).
  - External links waste context window tokens on every single turn.
  - Links rot and decay into 404s over time.
  - Executing agents do not browse the web during high-speed CLI coding tasks. An external link is 100% dead weight to an agent running a skill.
- **The rule**: If an operational detail, flag, syntax constraint, or edge case is required, **encode it directly as clear, actionable instruction or checkable verification criteria**. Never link out to it.

### High Signal-to-Noise 

Follow the repository's `unslop` and `simplicity` standards:
- **No AI filler or marketing adjectives**: Eliminate words like *robust*, *seamless*, *delve*, *pivotal*, *testament*, *landscape*, *crucial*, *holistic*.
- **No decorative clutter**: Do not use emojis in headings, callout boxes, or bullet points.
- **No em dashes**: Do not use em dashes (`—`) anywhere in prose. Use periods, commas, colons, or restructure the sentence directly.
- **Imperative voice**: Write direct, actionable commands (*"Inspect the staged diff"*, *"Check test coverage"*, never *"It is important to ensure..."*).
- **No conversational artifacts**: Exclude chatbot pleasantries, introductory summaries, sign-offs, and meta-commentary.
- **No "no-op" sentences**: Never waste tokens restating what models do by default. Every sentence must meaningfully constrain or steer agent behavior.
- **Observable exit criteria**: Every step or review phase must end with an unambiguous, checkable completion criterion.


### What Will Not Be Accepted 

- **Speculative or theoretical skills**: Every skill must address real, observable engineering workflows.
- **Fabricated content or invented facts**: Never invent flags, unsupported options, or fake benchmarks.
- **Bundled unrelated changes**: Never batch multiple unrelated skill edits into a single session or PR. Keep edits atomic.

---

## 3. Skill Architecture and Authoring Standards

### Directory Structure

```text
alfa-reza/skills/
├── README.md
├── AGENTS.md
├── LICENSE
└── skills/
    └── <skill-name>/
        ├── SKILL.md                  # Primary skill definition and instructions
        ├── references/               # (Optional) Deep references for progressive disclosure
        │   └── <topic>.md
        └── scripts/                  # (Optional) Deterministic helper scripts
```

### Frontmatter

Every `SKILL.md` must begin with valid YAML frontmatter containing:
- `name`: Lowercase kebab-case matching the directory name exactly (e.g., `code-review`, `commit`).
- `description`: A dense, action-oriented context pointer stating what the skill does, explicit triggers (when to use), and boundaries.

```yaml
---
name: example-skill
description: Brief description of what the skill does. Triggers on when to use, and boundaries.
---
```

### Progressive Disclosure and Information Hierarchy

- **Keep `SKILL.md` tight**: The main `SKILL.md` should be focused, legible, and directly actionable (prefer under 100 to 150 lines).
- **Use `references/` for deep material**: Move heavy edge cases, language-specific tables, or extensive auditing lenses into separate files under `references/<topic>.md`.
- **Inline what all runs need**: Keep core sequential steps and hard guardrails in `SKILL.md`. Push conditional or branch-specific material behind context pointers.

---

## 4. Repository Workflow and Conventions

### Readme Synchronization

When adding, renaming, or removing a skill:
- Update the top-level `README.md` Available Skills table with the skill name, short description, and links.
- Update the skills count badge in `README.md`.

### Git Commit Standards

All commits must strictly follow `skills/commit/SKILL.md`:
