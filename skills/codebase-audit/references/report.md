# Audit Report Guidance

Deliver clear, structured audit findings in conversation. Scale report depth to the selected audit mode (`quick`, `standard`, `deep`). Avoid filler text, speculative accusations, and arbitrary numerical health scores.

## Executive Summary and Baseline

Open the report with the operational baseline:

```markdown
# Codebase Audit Report: <Target Name or Path>

- **Audit Mode**: `quick` | `standard` | `deep`
- **Assumed Operational Load**: <e.g., Single-user CLI | Background worker | Multi-tenant API at ~500 req/s>
- **Target Revision / Scope**: <Commit hash, branch, or inspected directories>
- **Verdict**: <One-line status: Healthy, or top blocking remediation priorities>
```

## Triaged Findings

Group findings into three action-oriented tiers. Skip empty groups. Number findings sequentially across all tiers (1, 2, 3...) so developers can reference them easily (e.g., "fix 1 and 3").

### 1. Must Fix
Critical defects, exploitable security vulnerabilities, active data loss or corruption, and crashes or deadlocks under the assumed operational load.

### 2. Should Fix
High-risk code paths lacking regression tests, real performance bottlenecks on hot paths, architectural boundary leaks, and missing transaction or lock boundaries.

### 3. Nice to Have / Minor Suggestions
Small code improvements, non-blocking documentation discrepancies, and minor simplification opportunities.

---

## Finding Structure

Format every finding with these four concise elements:

```markdown
### <N>. <Concise Title Describing the Defect> (`<file_path>:<line_range>`)

- **What this is**: Concise context explaining the role of the component in the application flow.
- **Problem and causal path**: Triggering inputs, sequence of operations, or concurrency conditions causing failure. Include the specific line references and data flow.
- **Minimal root-cause fix**: Smallest complete code change that eliminates the root cause. Prefer deletion or standard-library usage over new frameworks or wrappers.
- **If we skip it**: Concrete operational failure, security exposure, or data corruption that occurs if left unfixed.
```

### Example Finding

```markdown
### 1. Billing Day Closure Uses Server Local Time (`billing/close_day.py:L40-52`)

- **What this is**: Nightly batch job responsible for finalizing daily transactions and issuing end-of-day billing records.
- **Problem and causal path**: Uses `datetime.now()` without timezone specification, taking the server UTC clock. Transactions executed at 00:30 local store time (UTC+7) are attributed to the previous billing cycle.
- **Minimal root-cause fix**: Pass the merchant store timezone explicitly: `datetime.now(ZoneInfo(store.timezone)).date()`.
- **If we skip it**: Transactions in early morning hours will be billed on the wrong calendar day, causing revenue recognition discrepancies.
```

---

## Needs Validation

List plausible security or reliability risks where static evidence indicates potential exposure, but complete end-to-end exploitability or runtime conditions cannot be fully proven from repository source alone.

For each item specify:
- Potential defect and affected component.
- Evidence observed in code.
- Missing evidence or runtime prerequisite required to confirm.
- Recommended verification action.

---

## Lean Ledger

When lean bloat findings exist, summarize the potential footprint reduction:

```markdown
## Lean Ledger

- **Dead Code**: Remove `<component>` (`<file_path>`, ~45 lines unused since v2 migration).
- **Native Replacement**: Replace `<library>` with runtime native API (`<native_feature>`, eliminates 1 external dependency).
- **YAGNI Abstraction**: Collapse single-implementation interface `<InterfaceName>` into `<ConcreteClass>` (~30 lines boilerplate removed).

**Total Potential Savings**: `-<N> lines, -<M> dependencies`
```

---

## Scope and Blindspots

Conclude with transparent accounting of audit boundaries:
- Inspected areas: Subsystems and entry points reviewed end-to-end.
- Sampled areas: Subsystems examined via spot-checks.
- Unexamined areas: Files, third-party vendored directories, or integration boundaries deliberately excluded, with stated reasons.
