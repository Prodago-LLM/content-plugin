---
name: check-op-mapping
description: Check that every CO statement requiring an OP has appropriate mappings (DoD Criterion 1)
argument-hint: <CO key, e.g. CO0104>
---

# Check OP Mapping Complete (Definition of Done — Criterion 1)

Validate that every CO statement requiring OP mapping has appropriate OPs assigned, the mappings are sufficient, and no duplicates exist.

## Input
CO key: $ARGUMENTS

## Step 1: Fetch CO Data via MCP

Use the available MCP tools to fetch:
1. The full CO hierarchy for the given CO key — all chapters, sections, and statements (level 1, 2, and 3 nodes)
2. For each statement, the list of OPs mapped to it (OP key, title, context, and any specifications)

Build the full picture: every leaf statement and its mapped OPs.

## Step 2: Classify Each Statement

For every **leaf statement** (level-3 nodes, or level-2 nodes with no children), determine whether it requires OP mapping.

Apply these rules:

**Does NOT require mapping:**
- Section headers, effective dates, authority references
- Definitions, scope/application statements, jurisdiction clauses
- Statements ending with ":" (introductions to sub-items)
- Pure objectives or expected results without explicit action obligations
- Cross-references to other articles or notes providing background context

**REQUIRES mapping:**
- Contains explicit action verbs: establish, define, implement, assess, monitor, document, train, notify, designate, conduct, maintain
- Specifies responsibilities with concrete tasks assigned to a party
- Requires governance activities, technical controls, or documentation
- Contains assessment, evaluation, or record-keeping requirements

## Step 3: Run the Three Checks

### Check 1 — Coverage: All Statements That Need OPs Have At Least One

For each statement classified as "requires mapping":
- If `ops` list is empty → **FINDING: Unmapped statement**

### Check 2 — Sufficiency: Mapped OPs Collectively Cover the Statement

For each statement that has mapped OPs:
1. Read the full statement text
2. Read the mapped OP titles, descriptions, and specifications
3. Assess whether the OPs collectively address ALL actionable requirements in the statement
4. If any requirements are left uncovered → **FINDING: Insufficient coverage** (describe the gap)

### Check 3 — Uniqueness: No Duplicate OP Mappings per Statement

For each statement that has mapped OPs:
- If any OP key appears more than once in the mapped list → **FINDING: Duplicate mapping**

## Output Format

```markdown
# OP Mapping Check — $ARGUMENTS

**CO:** [CO name]
**Date:** [today]
**Criterion:** 1 — OP Mapping Complete

**Overall: PASS / FAIL / NEEDS REVIEW**

## Summary

| Check | Status | Count |
|-------|--------|-------|
| Coverage (unmapped statements) | PASS/FAIL | X statements |
| Sufficiency (requirement gaps) | PASS/REVIEW | X statements |
| Uniqueness (duplicate mappings) | PASS/FAIL | X statements |

## Findings

### Unmapped Statements
[For each: statement code, statement text (truncated to 100 chars), reason mapping is needed]
[Or: "None found"]

### Insufficient Coverage
[For each: statement code, statement text, mapped OPs, and what requirement is not covered]
[Or: "None found"]

### Duplicate Mappings
[For each: statement code, duplicated OP key and title]
[Or: "None found"]

## Statistics
- Total leaf statements: X
- Statements requiring OP mapping: X
- Statements with OPs mapped: X
- Coverage rate: X%
- Distinct OPs mapped: X
- Total mappings: X
```

## Important Notes

- **Coverage and Uniqueness** are deterministic — they produce definitive PASS/FAIL
- **Sufficiency** requires judgment — flag as REVIEW if uncertain whether OPs fully cover a statement's requirements
- Do NOT include a per-statement classification table — only report findings (issues)
- A statement that requires mapping but has no OPs is always FAIL, not REVIEW
