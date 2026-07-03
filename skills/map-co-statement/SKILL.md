---
name: map-co-statement
description: Full mapping workflow — analyse a CO statement and find or draft OPs to cover it
argument-hint: <CO statement code, e.g. CO0001-L0020> or paste the statement text directly
---

# Map CO Statement to OPs

Run the full Statement-to-OP Mapping workflow for a given compliance statement. This is the primary tool for building or extending a compliance playbook.

## Input
CO statement code or statement text: $ARGUMENTS

## Overview

The mapping workflow has 6 phases. Run them in order. Do not skip phases.

```
Phase 1: Fetch & Assess Statement
Phase 2: Search Existing OPs
Phase 3: Match and Gap Analysis
Phase 4: Draft New OPs (if gaps remain)
Phase 5: Validate Redundancy
Phase 6: Output Results
```

---

## Phase 1: Fetch & Assess Statement

If a statement code was provided (e.g. CO0001-L0020), use MCP to fetch the full statement text, its parent CO, and the CO hierarchy context.

If statement text was pasted directly, identify the CO it belongs to if stated, or note it as standalone text.

**Determine if mapping is required.** A statement requires OP mapping when it contains actionable requirements. Apply these rules:

**Does NOT require mapping (skip to Phase 6 with "No mapping needed"):**
- Section headers, effective dates, authority references
- Definitions, scope statements, jurisdiction clauses
- Statements ending with ":" (role or section introductions)
- Pure objectives or recitals without action verbs
- Cross-references to other articles
- Background context without obligations

**REQUIRES mapping (proceed with workflow):**
- Contains explicit action verbs: establish, define, implement, assess, monitor, document, train, notify, designate, conduct, ensure, maintain
- Specifies responsibilities with concrete tasks
- Requires governance activities, technical controls, or documentation
- Contains assessment, evaluation, or record-keeping requirements

If no mapping is needed, explain why and stop.

---

## Phase 2: Search Existing OPs

Search the PrimeRise registry via MCP for OPs that could cover this statement's requirements.

**Search strategy — run all three in parallel:**

1. **Domain search** — identify the governance domain from the statement:
   - Map the statement's topic to the relevant IMR Value Groups (VG10–VG5)
   - Browse OPs in the relevant categories

2. **Keyword search** — extract the key verb + object pairs from the statement and search (e.g. "document personal data processing", "designate data protection officer")

3. **Synonym search** — try alternative phrasings for the same concept

For each candidate OP found, fetch: title, description, intended results, context (project/enterprise), and any existing mappings to this CO.

---

## Phase 3: Match and Gap Analysis

Decompose the statement into its **actionable requirements** — discrete obligations or activities it imposes.

For each requirement, assess whether an existing OP covers it:

| Coverage verdict | Criteria |
|-----------------|----------|
| **COVERED** | An existing OP's title and description clearly encompass this requirement |
| **COVERED WITH SPEC** | An existing OP covers it but needs a specification to capture the CO-specific angle |
| **GAP** | No existing OP covers this requirement |

**OP selection rules:**
- Prefer generic OPs over specific ones — OPs are reusable across COs
- One OP can be mapped to multiple statements; one statement can map to multiple OPs
- An OP must not be mapped twice to the same statement (merge specifications instead)
- If the statement has both a project-context and enterprise-context angle, both may be needed (separate OPs)

---

## Phase 4: Draft New OPs (for gaps)

For each identified gap, draft a new OP following the Prodago writing standards:

**Title:** [Imperative verb] + [Object] + [Optional context], 5–12 words
**Description:** Two sentences — gerund phrase + "This includes [sub-tasks]"
**Intended Results:** 1–3 passive-voice sentences focused on value/benefit

Also recommend:
- **IMR placement** — which Value Group → Category this OP would fall under
- **Context** — project or enterprise
- **PP phase** — which project phase (for project OPs), or "Organisation Requirements" (for enterprise OPs)

---

## Phase 5: Validate Redundancy

For each new OP drafted:
1. Search MCP again using the new OP's proposed title keywords
2. Confirm no existing OP in the same IMR category covers the same activity
3. If a near-duplicate is found, prefer the existing OP with a specification over creating a new one

---

## Phase 6: Output Results

```
## Statement Mapping: [Statement Code]

**CO:** [CO name]
**Statement:** [Full statement text]
**Mapping required:** Yes / No

---

### Existing OPs to Map

| OP Key | OP Title | Context | Coverage | Specification needed? |
|--------|----------|---------|----------|----------------------|
| OP#### | [title] | Project/Enterprise | COVERED / COVERED WITH SPEC | Yes/No |

### Specifications to Draft
[For each OP where a spec is needed — use /draft-specification to generate]

---

### New OPs to Create

#### New OP 1: [Proposed Title]
**Title:** [title]
**Description:** [two-sentence description]
**Intended Results:**
- [IR 1]
- [IR 2]
**Suggested IMR:** [Value Group → Category]
**Context:** Project / Enterprise
**PP Phase:** [Phase name]

---

### Summary
- Statements requiring mapping: 1
- Existing OPs identified: [n]
- Specifications needed: [n]
- New OPs to create: [n]
- Gaps remaining: None / [describe]
```

Now run the full mapping workflow for the provided statement.
