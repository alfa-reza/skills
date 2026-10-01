# Audit report guidance

Use the requested format and language. Scale detail to scope and risk. For a small audit, omit ceremony and present findings and material limitations. For larger audits, use the applicable sections below; combine or omit empty sections rather than padding the report.

## Summary and baseline

- Main risks and recommended first actions; if there are no established material findings, state that within the inspected scope.
- Target, revision, relevant working-tree state, base/head for branch audits, environments, requirements, assumptions, and exclusions. Distinguish new regressions from pre-existing defects.

## Findings

Order by severity, then practical urgency. Group related manifestations under one root cause; use stable IDs when multiple findings need tracking. For each finding include:

- Title, category, severity, confidence (high/medium), verification (source-verified/reproduced).
- Location: current path and line range or symbol; affected callers or platforms where relevant.
- Evidence: concise source or observed behavior, triggering conditions, causal path, and expected behavior or violated requirement. Redact sensitive values.
- Impact: realistic consequences, exposure, and affected scope; do not invent likelihood or quantitative estimates.
- Fix: actionable root-cause correction, necessary contract changes, and compatibility or remediation risks where relevant.
- Validation: focused regression test, static proof, or safe reproduction/check to verify the correction.

Combine fields into concise prose when clearer. Do not call a potential issue an established finding without adequate evidence.

## Needs validation

List plausible material risks whose core defect is not established, with supporting evidence, missing information, potential impact, and next verification step. Prioritize potentially severe risks without assigning them confirmed severity. Omit disproved claims and unsupported speculation.

## Verification and coverage

Record actual checks and relevant commands, results, failures, environment constraints, inspected/sampled/unexamined areas, and reasons for exclusions or unavailable checks. Distinguish source verification from runtime observation. Explain limits on any conclusions; unchecked areas have not passed. Never claim complete coverage or security without evidence.

## Remediation priorities

Recommend an order based on impact, urgency, dependencies, and change risk. Identify quick fixes versus structural work and migration/rollback implications when useful. Effort and savings estimates must have a stated basis; omit arbitrary health scores or LOC targets.

Return the report in conversation unless the user requests a different deliverable. Do not automatically implement fixes.
