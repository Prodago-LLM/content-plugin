---
name: draft-artefact
description: Suggest and draft an artefact (title + description) for an OP
argument-hint: <practice code>
---

# Draft Artefact for an OP

Decide whether the given OP should produce an artefact, determine the appropriate type, and draft a title and description following the Prodago artefact writing standards.

## Input
Practice code: $ARGUMENTS

## Step 1: Fetch the Practice via MCP

Use the available MCP tools to fetch the practice by the given code. Read:
- Title, description, intended results
- Any artefacts already linked to this practice
- Context (project or enterprise)

If the practice cannot be found, report this and stop.

## Step 2: Check Existing Artefacts

If the practice already has artefacts linked, list them. Ask the user whether they want to:
- Review the existing artefacts' quality (check title/description against standards)
- Draft an additional artefact
- Stop (artefacts are adequate)

## Step 3: Decide Whether an Artefact Is Needed

Apply this decision flow:

**Step 3a — Analyse the OP verb:**

| OP verb category | Artefact signal |
|-----------------|-----------------|
| Document, Record, Prepare, Compile, Report, Publish | Strong — the activity inherently produces a document |
| Establish, Define, Develop, Create, Design | Moderate — likely produces a policy, framework, or plan |
| Assess, Evaluate, Review, Audit, Analyse | Moderate — likely produces an assessment report |
| Implement, Execute, Conduct, Perform, Apply | Weak — may produce evidence of execution |
| Monitor, Track, Manage, Control, Maintain | Context-dependent — may produce a log or report |
| Train, Educate, Onboard | Attestation — completion is the evidence |
| Approve, Validate, Verify, Attest | Attestation — the decision/confirmation is the artefact |

**Step 3b — Consider governance evidence need:**
- Would an auditor, regulator, or reviewer ask "where is the evidence this was done?"
- Does a downstream OP depend on the output of this one?
- Is a formal record required for compliance demonstration?

**Classification:**
- **Document artefact** — the activity produces substantive documentation
- **Attestation artefact** — the activity confirms that something occurred or a condition is met
- **No artefact needed** — the activity is a pure process step with no tangible output or evidence requirement

If no artefact is needed, explain why and stop.

## Step 4: Search for an Existing Artefact via MCP

Before drafting a new artefact, search the MCP for existing artefacts with a similar title or purpose. If a suitable existing artefact is found:
- Report it (key, title, description)
- Recommend linking it to this OP rather than creating a new one
- Offer to draft a new one only if the existing artefact is genuinely different in scope

## Step 5: Draft the Artefact

### For Document Artefacts

**Title format:** Noun phrase — `[Document type] [Subject]`
- Good: "Data Quality Assessment Report", "AI Risk Register", "Privacy Impact Assessment"
- Bad: "Document for assessing data quality" (verb phrase), "Report" (too vague)

**Description formula (3–5 sentences, present tense):**
```
[What the artefact IS — definition]. [Why it exists — the problem it solves]. [Value statement]. This includes [specific contents]. [Usage context — who uses it and how].
```

**Rules:**
- Generic enough to apply across all OPs that may produce it (artefacts are reusable)
- No OP-specific language — the artefact title and description stand alone
- Present tense throughout
- 3–5 sentences

### For Attestation Artefacts

**Title format:**
- `Attestation - [Condition/State]` — e.g. "Attestation - Privacy Training Completed"
- `Attestation for "[OP Title]"` — e.g. `Attestation for "Evaluate competency level"`

Use the first pattern when the attestation captures a condition; use the second when it is specific to a single OP.

**Description:** Same 3–5 sentence formula, adapted for confirmation artefacts.

## Output Format

```
## Artefact Draft: [Practice Code]

**Practice:** [Practice title]

### Decision
**Type:** Document / Attestation / No artefact needed
[One sentence: why this artefact type is appropriate for this OP]

### Existing Artefact Check
[Report any existing artefacts found, or "No closely matching artefact found in PrimeRise."]

### Draft Artefact

**Title:** [Artefact title]

**Description:** [3–5 sentences following the formula]

### Linking Note
[Whether this should be a new artefact or link to an existing one; whether it should be mandatory or optional]
```

Now fetch the practice, apply the decision flow, and draft the artefact.
