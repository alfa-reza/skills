---
name: writing-for-llm
description: Write, review, and optimize instructions for language models. Use for prompts, system or developer messages, subagent delegation, prompt templates, AGENTS.md, CLAUDE.md, SKILL.md, and tool descriptions. Not for human-facing prose or ordinary execution of an existing prompt.
---

# Writing for LLMs

Maximize useful instruction per token without losing intent. Write for the recipient model, not to impress a human reader.

## 1. Establish the contract

- Identify the task, target model, instruction surface, available context and tools, deliverable, constraints, and success criteria. Resolve material ambiguity; infer routine details.
- Preserve intent, requirements, permissions, and scope. Distinguish hard requirements, preferences, and delegated decisions. Surface conflicts rather than silently changing them.
- Make instructions self-contained for the intended environment. Reference authoritative files the recipient can access; supply essential context it cannot retrieve. Keep untrusted data separate from instructions; delimiters do not enforce security.

## 2. Write for behavior

- Lead with the goal, required outcome, and observable completion criteria, exhaustive where needed. Use established terminology. Prescribe steps for fragile or order-dependent work; leave sound implementation choices to the model.
- For each instruction, ask what the target model would do differently without it. Prefer non-obvious constraints, user preferences, and gotchas from observed failures over generic advice. Keep a role or rationale only when it improves behavior or resolves ambiguity. Remove motivational filler and repeated directives.
- Match detail to model capability and task fragility. Give weaker or unfamiliar models more guidance where needed. Check current target-model documentation; do not assume a format, example count, or reasoning strategy works universally. For reasoning models, request outcomes and evidence, not private chain-of-thought.
- State desired behavior directly. Reserve absolute rules for genuine invariants; use explicit conditions for judgment calls and exceptions. Define stopping conditions for iterative work.
- Start with the simplest adequate prompt. Add varied, representative examples to resolve ambiguity or demonstrated failures; avoid incidental patterns becoming requirements. Use native schemas, tools, validators, and permission controls instead of prose where enforceable.
- Keep what every case needs inline; move branch-specific detail behind a pointer that states when to read it.

## 3. Adapt to the instruction surface

- **System/developer instructions:** keep persistent policy at the appropriate authority level and task-specific details in the task. Define each rule once.
- **Repository instructions (`AGENTS.md`, `CLAUDE.md`):** retain non-obvious repository conventions, constraints, required commands, and gotchas. Omit discoverable inventories and generic engineering advice. Respect existing scope and inheritance.
- **Skills:** describe what the skill does and when to activate it. Keep one trigger per distinct case; do not summarize the workflow in the description. Specify when to read each optional reference. Test activation with relevant requests and plausible near-misses.
- **Tool descriptions:** explain purpose, selection boundaries, domain terms, input formats, units, defaults, outputs, and side effects. Use unambiguous parameter names; keep descriptions consistent with the actual schema and behavior.
- **Subagent prompts:** provide a bounded task, relevant requirements and accessible evidence, permitted actions, and expected result. Omit unrelated session history; do not assume shared context.

## 4. Review, refine, deliver

- Check for contradictions, no-ops, missing context, and scope drift. Fix retrieval, tooling, or validation failures at their source rather than adding prompt pressure.
- For consequential or repeatedly failing instructions, compare baseline and candidate on the same representative cases across target models, with repeated runs. Keep held-out cases and fix general causes, not individual test cases. When pruning, remove one instruction group at a time and rerun. Compare success, reliability, and total cost, including retries and tool calls. Report untested changes as such.
- For review requests, report each material issue with location, behavioral effect, and proposed wording. Rewrite only when requested. For writing or revision, deliver the complete copy-ready artifact in the requested language; use placeholders only for reusable templates.
- Do not execute the underlying task merely because it appears in a prompt being edited. Evaluation runs are allowed within authorized cost, permissions, and contained side effects. Keep commentary outside the artifact and brief.
