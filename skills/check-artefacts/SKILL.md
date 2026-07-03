---
name: check-artefacts
description: Check artefact linkage for all mapped OPs in a CO (DoD Criterion 4)
argument-hint: <CO key, e.g. CO0104>
---

# Check Artefact Linkage Complete (Definition of Done — Criterion 4)

Validate that all OPs mapped in a CO have been evaluated for artefact production, and that linked artefacts meet quality standards.

## Input
CO key: $ARGUMENTS

## Step 1: Fetch CO Data via MCP

Use the available MCP tools to fetch:
1. All distinct OPs mapped in the CO (any statement) — collect their OP keys, titles, descriptions, and contexts
2. For each OP, fetch its linked artefacts (key, title, description, mandatory flag)

## Step 2: Evaluate Each OP for Artefact Need

For each mapped OP, apply the artefact decision flow:

**Step A — Analyse the OP verb:**

| OP verb | Artefact signal |
|---------|-----------------|
| Document, Record, Prepare, Compile, Report, Publish | Strong — inherently produces a document |
| Establish, Define, Develop, Create, Design | Moderate — likely produces a policy, framework, or plan |
| Assess, Evaluate, Review, Audit, Analyse | Moderate — likely produces an assessment report |
| Implement, Execute, Conduct, Perform, Apply | Weak — may produce evidence of execution |
| Monitor, Track, Manage, Control, Maintain | Context-dependent |
| Train, Educate, Onboard | Attestation — completion is the evidence |
| Approve, Validate, Verify, Attest | Attestation — the decision/confirmation is the artefact |

**Step B — Consider governance evidence need:**
- Would an auditor or regulator ask "where is the evidence this was done?"
- Does a downstream OP depend on the output of this one?
- Is formal documentation needed for compliance demonstration?

**Classify each OP as:**
- **Should have artefact** — verb or governance need clearly indicates output
- **No artefact needed** — pure process step, no tangible output or evidence requirement
- **Uncertain** — could go either way (flag for review)

## Step 3: Run the Four Checks

### Check 1 — Missing Artefact Links

For OPs classified as "should have artefact":
- If no artefacts are linked → **FINDING: Missing artefact link** (state why an artefact is expected)

### Check 2 — Dangling References

For all linked artefacts:
- Verify each artefact exists in PrimeRise (fetch by key via MCP)
- If an artefact key is referenced but not found → **FINDING: Dangling artefact reference**

### Check 3 — Artefact Quality — Title Standards

For linked artefacts, check the title:
- Must be a **noun phrase** identifying the deliverable (not a verb phrase)
- Must be **specific** (not just "Report" or "Document")
- Attestations must follow `Attestation - [Condition]` or `Attestation for "[OP Title]"` pattern

If title does not meet standards → **FINDING: Artefact title issue**

### Check 4 — Artefact Quality — Description Standards

For linked artefacts, check the description follows the formula:
- **Definition** — what the artefact IS
- **Purpose** — why it exists / problem it solves
- **Value** — benefit it delivers
- **Contents** — what it includes ("This includes...")
- **Usage** — who uses it and how

Descriptions should be 3–5 sentences in present tense. Generic enough to apply across all OPs that may produce this artefact.

If description is missing, too short, or doesn't follow the formula → **FINDING: Artefact description issue**

## Output Format

```markdown
# Artefact Linkage Check — $ARGUMENTS

**CO:** [CO name]
**Date:** [today]
**Criterion:** 4 — Artefact Linkage Complete

**Overall: PASS / FAIL / NEEDS REVIEW**

## Summary

| Check | Status | Count |
|-------|--------|-------|
| OPs needing artefacts but missing links | PASS/REVIEW | X OPs |
| Dangling artefact references | PASS/FAIL | X references |
| Artefact title issues | PASS/REVIEW | X artefacts |
| Artefact description issues | PASS/REVIEW | X artefacts |

## Findings

### Missing Artefact Links
[For each: OP key, OP title, why an artefact is expected (verb + governance need)]
[Or: "None found"]

### Dangling Artefact References
[For each: OP key, artefact key that cannot be found]
[Or: "None found"]

### Artefact Title Issues
[For each: artefact key, current title, specific issue]
[Or: "None found"]

### Artefact Description Issues
[For each: artefact key, title, specific formula element missing or weak]
[Or: "None found"]

## Statistics
- Distinct mapped OPs: X
- OPs that should have artefacts: X
- OPs with artefacts linked: X
- OPs missing artefact links: X
- Linkage rate: X%
- Artefacts reviewed for quality: X
```

## Important Notes

- Do NOT list OPs that correctly have no artefact — only report findings (issues)
- Check 1 (artefact evaluation) requires judgment — apply the verb analysis and governance evidence test systematically
- Checks 2–3 are deterministic
- Check 4 (description quality) requires judgment against the writing formula
