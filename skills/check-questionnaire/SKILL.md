---
name: check-questionnaire
description: Check questionnaire coverage — every project-context OP reachable through trigger logic (DoD Criterion 3)
argument-hint: <CO key, e.g. CO0104>
---

# Check Questionnaire Complete (Definition of Done — Criterion 3)

Validate that every project-context OP mapped in a CO playbook is reachable through at least one inclusion path in the questionnaire, and that the questionnaire is internally consistent.

This skill replicates the exact logic of the `check_dod3_questionnaire.py` validator. All checks are deterministic set and graph operations — execute them precisely.

## Input
CO key: $ARGUMENTS

## Scope

Enterprise-context OPs are **out of scope**: they apply organization-wide and must not be present in the questionnaire's opIndex. Only project-context OPs must be reachable through the questionnaire.

---

## Step 1: Fetch Playbook Data via MCP

Use the available MCP tools to fetch the CO's mapped practices. For each OP mapped to any statement in this CO, collect:
- `op_key` — the practice code (e.g. OP1234)
- `statement_code` — the CO statement it maps to (e.g. CO0001-L0020)
- `context` — "project" or "enterprise"
- `title` — the practice title

Build these sets:
- **`project_pairs`** = set of `(op_key, statement_code)` where context = "project"
- **`enterprise_pairs`** = set of `(op_key, statement_code)` where context = "enterprise"
- **`statement_codes`** = set of all statement codes present in the CO hierarchy
- **`op_meta`** = dict of `{op_key: {title, context}}` (first-seen context wins if duplicates)
- **`unknown_context`** = list of `(op_key, statement_code, raw_context)` where context is neither "project" nor "enterprise"

---

## Step 2: Fetch Questionnaire Data via MCP

Use the available MCP tools to fetch the questionnaire for this CO. This requires:

### 2a — Questions and Trigger Structure

For each question in this CO's questionnaire, collect:
- `question_code` — the question identifier (e.g. Q8)
- `text` — the question text
- `triggered_by` — list of `{question_code, answer_code}` pairs that trigger this question (null/empty = root question)
- `filter_on_qa_id` — an answer code that, when selected, hides this question (null = no filter)
- `answers` — dict of `{answer_code: {text, triggers: [question_codes...]}}` where `triggers` = list of question codes this answer activates

Build:
- **`questions`** = dict of all questions with the above structure
- **`answer_triggers`** = dict of `{answer_code: [question_codes]}` — which questions each answer triggers
- **`question_triggered_by`** = dict of `{question_code: [answer_codes]}` — which answers trigger each question

### 2b — OP Prescriptions (AnswerPracticeMapping via PolicyPracticeDetail)

For each AnswerPracticeMapping entry for this CO, collect:
- `answer_code` — the questionnaire answer (e.g. Q8-A1)
- `op_key` — the prescribed practice (via PolicyPracticeDetail)
- `statement_code` — the CO statement (via PolicyPracticeDetail)
- `action` — "include" or "exclude"

Build:
- **`include_pairs`** = set of `(op_key, statement_code)` where action = "include"
- **`exclude_pairs`** = set of `(op_key, statement_code)` where action = "exclude"
- **`op_index`** = dict of `{op_key: {title, prescribedBy: [{action, answer_code, co_statements: [...]}]}}` — the inverted index of which answers prescribe each OP

---

## Step 3: Run All 10 Checks

Execute each check precisely. Collect all findings before reporting.

### Check 1 — Missing Inclusion Paths

```
missing_inclusion = project_pairs - include_pairs
```

Every `(op_key, statement_code)` pair in `project_pairs` must appear at least once in `include_pairs`. Any pair not found is a **FINDING: Missing inclusion path**.

### Check 2 — Missing Per-Mapping Paths

For project-context OPs mapped to **more than one statement**:
- Group `project_pairs` by `op_key` → `{op_key: set(statement_codes)}`
- For OPs with `len(statement_codes) > 1` only:
  - Find statements where `(op_key, statement_code)` is NOT in `include_pairs`
  - If any → **FINDING: Missing per-mapping path** (report OP key, title, which statements are missing, and which are covered)

Note: Check 2 is a refinement of Check 1 for multi-statement OPs. Single-statement OPs are fully covered by Check 1.

### Check 3 — Circular Triggers

Perform a depth-first search (DFS) over the downstream trigger graph:
- Graph: `{question_code: [downstream_question_codes]}` — downstream = questions triggered by any answer of this question
- For each unvisited node, DFS and track the current path (GRAY nodes)
- If a GRAY node is encountered → cycle detected
- Canonicalize cycles to avoid duplicates: for a cycle `A→B→C→A`, rotate so the lexicographically smallest node is first
- Record each unique cycle as a list of question codes → **FINDING: Circular trigger**

### Check 4 — Broken Chains

For every `triggered_by` entry in every question:
- Verify the `source_question_code` exists in `questions`
- Verify the `source_answer_code` exists in the answers of that source question
- If either check fails → **FINDING: Broken chain** (report target question, source question, source answer, reason)

### Check 5 — Invalid Statement References

For every `(op_key, statement_code)` pair in `include_pairs` and `exclude_pairs` (from opIndex/AnswerPracticeMapping):
- Verify `statement_code` exists in `statement_codes` (the CO's actual hierarchy)
- If not found → **FINDING: Invalid statement reference** (report op_key, statement_code, action)

### Check 6 — Invalid filterOnQaId References

For every question that has a `filter_on_qa_id` value:
- Parse the filter: format is `"Q8-A1"` (question code dash answer code)
- Verify the question code part exists in `questions`
- Verify the answer code exists in that question's answers
- If either check fails → **FINDING: Invalid filter reference** (report question, filter value, reason)

### Check 7 — Orphan Prescriptions

```
orphan_op_keys = op_index.keys() - op_meta.keys()
```

OPs present in the questionnaire opIndex but absent from the playbook entirely → **FINDING: Orphan prescription** (list each op_key)

### Check 8 — Over-Prescriptions

```
all_playbook_pairs = project_pairs | enterprise_pairs
over_prescriptions = {
    (op_key, stmt) for (op_key, stmt) in include_pairs
    if op_key in op_meta and (op_key, stmt) not in all_playbook_pairs
}
```

OP is in the playbook (any context) but the specific `(op, statement)` pair is not → **FINDING: Over-prescription** (report op_key, title, statement_code)

### Check 9 — Enterprise OPs in opIndex

```
enterprise_op_keys = {k for k, m in op_meta.items() if m["context"] == "enterprise"}
enterprise_in_opindex = op_index.keys() ∩ enterprise_op_keys
```

Enterprise-context OPs must never appear in the questionnaire opIndex → **FINDING: Enterprise OP in opIndex** (list each)

### Check 10 — Unknown OP Context

Any OP in `unknown_context` (context is neither "project" nor "enterprise") → **FINDING: Unknown OP context** (data integrity warning)

---

## Step 4: Compute Statistics

```
distinct_project_ops = set of op_key values from project_pairs
distinct_enterprise_ops = set of op_key values from enterprise_pairs
project_ops_missing_from_qn = distinct_project_ops - op_index.keys()
triggered_questions = questions where triggered_by is non-empty
root_questions = questions where triggered_by is null/empty
```

---

## Step 5: Output Report

```markdown
# Questionnaire Check — $ARGUMENTS

**CO:** [CO name]
**Date:** [today]
**Criterion:** 3 — Questionnaire Complete

**Overall: PASS / FAIL**

## Summary

| Check | Status | Count |
|-------|--------|-------|
| Missing inclusion paths | PASS/FAIL | X |
| Missing per-mapping paths | PASS/FAIL | X |
| Circular triggers | PASS/FAIL | X |
| Broken chains | PASS/FAIL | X |
| Invalid statement references | PASS/FAIL | X |
| Invalid filterOnQaId references | PASS/FAIL | X |
| Orphan prescriptions (OP in qn, not in playbook) | PASS/FAIL | X |
| Over-prescriptions (extra statement for known OP) | PASS/FAIL | X |
| Enterprise OPs present in opIndex | PASS/FAIL | X |
| Data integrity (unknown OP context) | PASS/FAIL | X |

## Findings

### Missing Inclusion Paths
[Table: OP key, OP title, Statement code]
[Project-context OP-to-statement mappings in the playbook but with no include action in the questionnaire]
[Or: "None found"]

### Missing Per-Mapping Paths
[Table: OP key, OP title, Missing statements, All statements]
[Project-context OPs mapped to multiple statements where some statements lack a dedicated inclusion path]
[Or: "None found"]

### Circular Triggers
[List each cycle as: Q1 → Q2 → Q3 → Q1]
[Or: "None found"]

### Broken Chains
[Table: Target question, Source question, Source answer, Reason]
[Or: "None found"]

### Invalid Statement References
[Table: OP key, Statement code, Action]
[coStatements values in the questionnaire that do not exist in the playbook hierarchy]
[Or: "None found"]

### Invalid filterOnQaId References
[Table: Question, Filter value, Reason]
[Or: "None found"]

### Orphan Prescriptions
[List: OP key — OP title]
[OPs in questionnaire opIndex but absent from the playbook entirely]
[Or: "None found"]

### Over-Prescriptions
[Table: OP key, OP title, Statement code]
[OP is in the playbook but not for that particular statement]
[Or: "None found"]

### Enterprise OPs in opIndex
[List: OP key — OP title]
[Enterprise-context OPs should never appear in the questionnaire opIndex]
[Or: "None found"]

### Data Integrity — Unknown OP Context
[Table: OP key, Statement code, Raw context value]
[Or: "None found"]

## Statistics
- Total OP-to-statement mappings in playbook: X
- Enterprise mappings (excluded from coverage check): X
- Project mappings (in scope): X
- Distinct OPs in playbook: X (project: X, enterprise: X)
- OPs in questionnaire opIndex: X
- Project OPs in playbook but NOT in questionnaire: X
- Questionnaire questions: X
- Triggered questions (have upstream dependencies): X
- Root questions (no triggers, always shown): X
```

## Execution Notes

- All 10 checks are **deterministic** — no judgment calls
- Run all checks against the same fetched data; do not re-fetch between checks
- Check 2 is only for OPs with >1 statement; Check 1 already handles single-statement OPs
- For Check 3 (cycle detection), canonicalize cycles before deduplication to avoid reporting the same cycle multiple times
- The overall result is FAIL if **any** check has findings; PASS only if all 10 return zero findings
