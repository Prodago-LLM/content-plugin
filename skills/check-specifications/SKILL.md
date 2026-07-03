---
name: check-specifications
description: Check specification quality across all OP-to-statement mappings in a CO (DoD Criterion 2)
argument-hint: <CO key, e.g. CO0104>
---

# Check Specifications Complete (Definition of Done — Criterion 2)

Validate that every OP-to-statement mapping has been evaluated for specification need, and that existing specifications are actionable and non-redundant.

## Input
CO key: $ARGUMENTS

## Step 1: Fetch CO Data via MCP

Use the available MCP tools to fetch:
1. The full CO hierarchy with all mapped OPs
2. For each OP-to-statement mapping: the statement text, OP title, OP description, and the specification text (if any)

## Step 2: Run the Three Checks

### Check 1 — Evaluated: Every Mapping Assessed for Specification Need

For each statement with mapped OPs, examine each OP-statement pair:

1. Read the statement text (the CO requirement)
2. Read the OP title and description
3. Ask: **Does the CO statement add specific conditions, scope, timing, methods, roles, or mandatory language that the generic OP title and description do not already capture?**
   - If YES → a specification is needed
   - If NO → no specification needed (the OP is self-explanatory in this context)

Apply the specification decision framework:

| Factor | Specification more likely needed | Less likely needed |
|--------|----------------------------------|--------------------|
| Statement type | Single OP for this statement (~89% need specs) | Multiple OPs share the statement (~50%) |
| OP role | This OP is the primary executor | Supporting/adjacent role |
| Statement specificity | Has timing, mandatory language, thresholds | Broadly matches OP title |

If a specification IS needed but the `specifications` field is empty → **FINDING: Missing specification**

### Check 2 — Quality: Specifications Are Actionable

For each mapping where a specification exists:

1. Is the specification text actionable — does it tell the practitioner specifically HOW to execute the OP in this CO context?
2. Does it go beyond restating the statement or OP title?
3. Does it use the correct format (Markdown bullet points starting with `+`)?

If a specification is a restatement, is vague, or lacks actionable guidance → **FINDING: Weak specification** (quote the problematic text)

### Check 3 — Non-Redundant: Specifications Add Value

For each mapping where a specification exists:

1. Is the specification content already fully obvious from the OP title and description alone?
2. Is the same guidance already covered by another OP's specification for the same statement?

If the specification adds no value → **FINDING: Redundant specification**

## Output Format

```markdown
# Specifications Check — $ARGUMENTS

**CO:** [CO name]
**Date:** [today]
**Criterion:** 2 — Specifications Complete

**Overall: PASS / NEEDS REVIEW**

## Summary

| Check | Status | Count |
|-------|--------|-------|
| Missing specifications | PASS/REVIEW | X mappings |
| Weak specifications | PASS/REVIEW | X mappings |
| Redundant specifications | PASS/REVIEW | X mappings |

## Findings

### Missing Specifications
[For each: statement code (truncated text), OP key, OP title, WHY a specification is needed here]
[Or: "None found"]

### Weak Specifications
[For each: statement code, OP key, current specification text, what is wrong with it]
[Or: "None found"]

### Redundant Specifications
[For each: statement code, OP key, specification text, why it is redundant]
[Or: "None found"]

## Statistics
- Total OP-to-statement mappings: X
- Mappings with specifications: X
- Mappings without specifications: X
- Specification rate: X%
```

## Important Notes

- All three checks require **judgment** — there are no purely deterministic rules for specification necessity
- Flag as REVIEW rather than FAIL when uncertain
- A specification is most clearly needed when the CO statement adds conditions or scope limitations not conveyed by the OP title
- A specification is clearly NOT needed when the OP title exactly restates what the statement requires
- Do NOT list every mapping — only report findings (issues)
