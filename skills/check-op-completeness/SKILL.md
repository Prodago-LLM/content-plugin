---
name: check-op-completeness
description: Check OP metadata completeness — PP, OVP, AM mappings, descriptions, IRs (DoD Criterion 5)
argument-hint: <CO key, e.g. CO0104>
---

# Check OP Completeness (Definition of Done — Criterion 5)

Validate that all OPs mapped in a CO have the required metadata and comply with writing standards.

## Input
CO key: $ARGUMENTS

## Step 1: Fetch CO Data via MCP

Use the available MCP tools to fetch:
1. All distinct OPs mapped in the CO — collect OP keys and titles
2. For each OP, fetch its full metadata:
   - Description and intended results (IRs)
   - Project phase mapping (PP)
   - OVP (Oversight Value Perspective) assignments
   - AM (Accountability Matrix) assignments — accountable and responsible functions
   - Context (project or enterprise)

## Step 2: Run the Five Checks

### Check 1 — PP Mapping: Each OP Assigned to Exactly One Project Phase

For each mapped OP:
1. Check whether it has a project phase assignment
2. If missing → **FINDING: Missing PP mapping**
3. If multiple phases assigned → **FINDING: Multiple PP mappings** (should be exactly one)
4. Cross-validate context:
   - Enterprise-context OPs → must map to "Organisation Requirements" (PPD9045)
   - Project-context OPs → must map to an appropriate project lifecycle phase (not Organisation Requirements)
   - Mismatch → **FINDING: Incorrect phase for context**

### Check 2 — OVP Mapping: Each OP Has OVP Assignments

For each mapped OP:
1. Check whether it has at least one OVP assignment
2. If missing → **FINDING: Missing OVP mapping**

### Check 3 — AM Mapping: Each OP Has 1 Accountable and 1–3 Responsible Functions

For each mapped OP:
1. Count Accountable assignments — must be exactly 1
2. Count Responsible assignments — must be 1 to 3
3. Assignments must be Functional Areas, not Leadership Areas

Findings:
- Missing accountable → **FINDING: Missing accountable function**
- Missing responsible → **FINDING: Missing responsible function**
- More than 3 responsibles → **FINDING: Excessive responsible functions**
- More than 1 accountable → **FINDING: Multiple accountable functions**

### Check 4 — Description Format

For each mapped OP, validate the description against writing standards:

| Rule | Pass condition |
|------|----------------|
| Two sentences | Exactly two sentences (period-separated) |
| Gerund opening | First sentence starts with a gerund (-ing form) matching the title verb |
| "This includes" | Second sentence starts with "This includes" |
| Sub-task form | Sub-tasks in "This includes" clause use present participle (-ing) |
| Tone | No first/second person pronouns ("you", "we", "I") |
| Not empty | Description is not empty or placeholder text |

If any rule fails → **FINDING: Description format issue** (specify which rule)

Note: Flag legacy descriptions (substantive but wrong format) as REVIEW, not FAIL. Flag empty descriptions as FAIL.

### Check 5 — Intended Results Format

For each mapped OP, validate the IRs:

| Rule | Pass condition |
|------|----------------|
| Has IRs | At least one IR exists |
| Passive ending | Each IR ends with a passive verb (is established, is ensured, are aligned, etc.) |
| Not a title restatement | IR does not simply restate the OP title in passive voice |
| One sentence | Each IR is a single sentence |

If any rule fails → **FINDING: IR format issue** (specify which rule)

## Output Format

```markdown
# OP Completeness Check — $ARGUMENTS

**CO:** [CO name]
**Date:** [today]
**Criterion:** 5 — OP Completeness

**Overall: PASS / FAIL / NEEDS REVIEW**

## Summary

| Check | Status | Issues |
|-------|--------|--------|
| PP mapping | PASS/FAIL | X OPs with issues |
| OVP mapping | PASS/FAIL | X OPs missing |
| AM mapping | PASS/FAIL | X OPs with issues |
| Description format | PASS/REVIEW | X OPs with issues |
| IR format | PASS/REVIEW | X OPs with issues |

## Findings

### PP Mapping Issues
[For each: OP key, OP title, specific issue (missing/multiple/wrong phase for context)]
[Or: "None found"]

### OVP Mapping Issues
[For each: OP key, OP title]
[Or: "None found"]

### AM Mapping Issues
[For each: OP key, OP title, specific issue]
[Or: "None found"]

### Description Format Issues
[For each: OP key, OP title, current description excerpt, specific rules violated]
[Or: "None found"]

### IR Format Issues
[For each: OP key, OP title, IR text, specific rules violated]
[Or: "None found"]

## Statistics
- Distinct mapped OPs evaluated: X
- OPs with complete metadata (PP + OVP + AM): X / X
- OPs with compliant descriptions: X / X
- OPs with compliant IRs: X / X
- OPs with empty/placeholder descriptions: X
- OPs with empty IRs: X
```

## Important Notes

- **PP, OVP, AM checks** are deterministic — the mapping either exists or it doesn't
- **Description and IR checks** are mixed: format rules are deterministic, quality is judgment
- Do NOT include a per-OP detail table — only report findings (issues)
- OPs with completely empty descriptions or IRs → FAIL; legacy format issues → REVIEW
