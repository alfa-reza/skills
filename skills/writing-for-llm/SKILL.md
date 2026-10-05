---
name: writing-for-llm
description: Guide language models in writing high-signal prompts, system instructions, and PRDs designed for downstream LLM execution. Use when crafting prompts, asking an LLM to draft a machine-executable PRD, or structuring instructions for coding agents.
---

# Writing for LLMs

Stop. Never lecture language models on basic programming or domain concepts. Models already possess pre-trained domain knowledge. High-signal instructions do not teach; they constrain, steer, set non-goals, and establish checkable verification criteria.

Maximize actionable guidance per token. Write for the recipient model, not to impress a human reader.

## 1. Core Principles: Steering over Lecturing

- **Eliminate no-op sentences:** Never write "ensure high quality", "be robust", or "think carefully". If an instruction does not meaningfully alter model behavior or rule out plausible failure modes, delete it.
- **State non-goals explicitly:** Models over-build by default. Explicitly listing what NOT to do ("No admin UI, no rate limiter in this iteration, no new dependencies") saves more tokens and prevents more regressions than paragraphs of positive requirements.
- **Define observable completion criteria:** Every task or requirement must specify what a passing check looks like: an exact CLI exit code, HTTP status, database row count, or output schema.
- **Match density to model capability:** Strong models need only concise boundaries and schemas. Weaker models need explicit operational steps and edge-case negative bounds. Test prompts against smaller models to uncover latent ambiguities.

## 2. Instruction Types and Surfaces

### A. Writing PRDs for Downstream LLMs (Spec-Driven Development)

When instructing an LLM to author a Product Requirement Document (PRD) for another LLM to implement, write it as a machine-executable contract, not a human narrative or business pitch:

1. **Numbered atomic requirements:** Assign a stable ID per requirement (`R1`, `R2`, `R3`). Write one atomic, testable sentence per requirement.
2. **Executable passing criteria:** Under every requirement, provide an explicit `Passes:` line specifying the exact verification check the executing model can test (HTTP status, database state, CLI exit code, or rendered UI element).
3. **Explicit non-goals:** Explicitly list what the implementing model must NOT build (no unrequested rate limiters, no extra admin panels, no premature abstractions).
4. **Data contracts and exact schemas:** Provide exact JSON structures, database schemas, and API request/response payloads rather than prose descriptions.
5. **Frozen baseline and scope constraints:** Anchor the PRD to the current repository state and explicitly name files or directories the executing model must not touch.
6. **Open questions rule:** Any unresolved question must include a named decision owner and the hard directive: "Until decided, do not implement." Unresolved questions without this directive invite agent hallucinations.

### B. Prompts and Prompt Templates for LLMs

When authoring prompts for an LLM to execute a task:
- **Goal first:** Lead with the required outcome, format, and hard boundaries.
- **Output contracts:** Specify exact markdown, JSON, or code block templates. Use placeholders (`<value>`) only for reusable templates.
- **Few-shot examples:** Include minimal, contrasting examples only when format or classification is subtle. Avoid accidental patterns becoming unintended constraints.

### C. System and Developer Instructions

- **Persistent invariants:** Reserve system instructions for persistent behavioral boundaries, security constraints, and operational rules.
- **Single authority level:** Avoid repeating rules across layers. Keep global policies in system instructions and task-specific parameters in user prompts.

### D. Subagent Task Delegation

When delegating tasks from one LLM agent to a downstream subagent:
- **Self-contained scope:** Provide a bounded task, accessible file paths, concrete goal, and expected return format.
- **Omit chat history:** Never forward raw conversation transcripts or conversational pleasantries; supply only relevant evidence and constraints.
- **Tool permissions:** Explicitly limit which tools and commands the subagent is authorized to use.

### E. Tool and Schema Descriptions

When defining tool descriptions that an LLM agent inspects to decide tool calls:
- **Precise parameter names:** Name parameters by semantic purpose. Document units (e.g., `timeoutMs`), accepted enums, edge-case defaults, and side effects.
- **Selection boundaries:** State clearly when the tool should NOT be used to prevent model tool-choice thrashing.

## 3. Review and Refine Protocol

- **Audit for fluff:** Strip conversational pleasantries ("Please", "I would like you to"), apologies, and decorative formatting.
- **Check for contradictions:** Ensure negative bounds do not conflict with required behaviors.
- **Prune incrementally:** When optimizing prompts, remove one instruction cluster at a time and evaluate against representative test cases.
- **Verify with light models:** A prompt that guides a lightweight model to clean compliance will perform flawlessly on frontier models.
