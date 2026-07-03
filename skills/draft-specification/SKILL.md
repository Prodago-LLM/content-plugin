---
name: draft-specification
description: Draft a CO-specific specification for an OP-to-statement mapping
argument-hint: <practice code> <CO statement code, e.g. CO0001-L0020>
---

# Draft OP Specification

Draft a specification — CO-specific execution guidance — for a given OP mapped to a given CO statement. Specifications tell practitioners HOW to execute the OP in a way that satisfies this particular compliance requirement.

## Input
Practice code and CO statement code: $ARGUMENTS

Parse the input: the first token is the practice code (e.g. OP2040), the second is the CO statement code (e.g. CO0001-L0020).

## Step 1: Fetch Data via MCP

Use the available MCP tools to retrieve:
1. **The practice** — fetch by the given practice code. Read its title, description, and intended results.
2. **The CO statement** — fetch the compliance object statement by the given statement code. Read the full statement text, which is the regulatory/standard requirement.
3. **Any existing specification** for this OP-statement pair — check if one already exists.

If the practice or statement cannot be found, report this and stop.

## Step 2: Decide Whether a Specification Is Needed

A specification is **needed** when the CO statement imposes specific conditions, scope, timing, methods, or mandatory language that the generic OP title and description do not already capture.

Apply this decision framework:

| Factor | Specification More Likely Needed | Specification Less Likely Needed |
|--------|----------------------------------|----------------------------------|
| Statement type | Single OP mapped to this statement | Multiple OPs mapped to this statement |
| OP role | This OP is the primary executor of the requirement | This OP plays a supporting role |
| Statement specificity | Statement has timing, methods, mandatory language | Statement is broadly aligned with OP title |
| Content already covered | OP description does NOT cover the CO's specific angle | OP title + description already convey the requirement |

**If no specification is needed:** explain why (the OP title and description already adequately convey how to execute the practice in the context of this CO statement) and stop.

**If a specification exists and is adequate:** report that it exists, quote it, and note it does not need to be replaced.

## Step 3: Draft the Specification

A specification provides **CO-specific execution guidance** — concrete, actionable instructions for HOW to perform this OP in a way that satisfies this particular statement.

**Format rules:**
- Use Markdown bullet points starting with `+`
- Extract specific requirements directly from the CO statement text
- Be actionable — not a restatement of the statement or OP title
- Be concrete — name specific deliverables, criteria, roles, or thresholds where the statement provides them
- Maintain a professional, objective tone

**What to extract from the CO statement:**
- Specific actions required (e.g. "must document the legal basis")
- Scope limitations (e.g. "applies only to automated decisions with legal effects")
- Timing requirements (e.g. "prior to data collection")
- Mandatory language or thresholds (e.g. "within 72 hours")
- Roles explicitly named (e.g. "data protection officer must be involved")
- Specific outputs required (e.g. "written record must be maintained")

**What NOT to include:**
- Content already obvious from the OP title and description
- Generic governance advice not specific to this CO statement
- Restatements of the OP title in different words

## Output Format

```
## Specification: [Practice Code] → [Statement Code]

**Practice:** [Practice title]
**Statement:** [Statement code] — [Statement name or brief label]
**CO:** [CO name]

### Decision
[One sentence: why a specification IS needed here — what the CO statement adds beyond the generic OP]

### Draft Specification

+ [Specific instruction 1 derived from CO statement]
+ [Specific instruction 2 derived from CO statement]
+ [Specific instruction 3 derived from CO statement]
[...as many as needed — no fixed count]

### Rationale
[1–2 sentences explaining what the specification captures that the OP description alone does not]
```

## Example

**Practice:** OP0674 — Document personal data processing activities
**Statement:** CO0001-L0050 (GDPR Art. 30 — Records of processing activities)

**Draft Specification:**
```
+ Document the name and contact details of the controller and, where applicable, the joint controller, the controller's representative, and the data protection officer.
+ Record the purposes of the processing for each activity.
+ Describe the categories of data subjects and categories of personal data processed.
+ Identify recipients of the personal data, including third countries or international organisations.
+ Where applicable, document transfers to third countries and the safeguards in place.
+ Retain the record in writing (including electronic form) and make it available to the supervisory authority on request.
```

Now fetch the practice and statement, decide whether a specification is needed, and draft it.
