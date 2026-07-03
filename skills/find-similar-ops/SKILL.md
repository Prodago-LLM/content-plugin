---
name: find-similar-ops
description: Search for existing OPs similar to an activity description to avoid duplication
argument-hint: <description of the activity or OP you want to create>
---

# Find Similar Existing OPs

Search PrimeRise for existing Operational Practices (OPs) that are similar to the described activity. Use this before creating a new OP to avoid duplication.

## Input
Activity or OP description: $ARGUMENTS

## Why This Matters

The Prodago registry contains 2,800+ OPs organised in a 5-level IMR taxonomy. Before creating any new OP, you MUST check whether a suitable OP already exists. Creating duplicate OPs fragments the registry and breaks the reuse principle.

## Step 1: Understand the Activity

Read the description and extract:
- **Core action verb** — what is being done (e.g. assess, monitor, define)
- **Object** — what is being acted on (e.g. AI model, personal data, data quality)
- **Governance domain** — which IMR Value Group(s) this falls under:

| Value Group | Key Domains |
|-------------|-------------|
| VG10 Projects | Project management, deliverables |
| VG11 Data | Data governance, metadata, architecture, sharing |
| VG12 Products | AI, GenAI, LLM, analytics, metrics |
| VG13 Cloud | Cloud computing governance |
| VG14 Risks | AI risks, model risks, privacy risks, security risks |
| VG2 Quality | Quality framework, management, reference data |
| VG3 Privacy | Privacy rights, obligations, breach, PIA |
| VG4 Security | Security governance, systems, information security |
| VG5 Ethics | Ethics governance, explainability, fairness |

- **Context** — is this project-level (per initiative) or enterprise-level (org-wide)?

## Step 2: Search via MCP

Use the available MCP tools to search for existing practices. Try multiple search strategies:

1. **By keyword** — search using the core action verb + object (e.g. "assess AI model bias")
2. **By domain** — browse practices in the identified Value Group(s) and relevant IMR categories
3. **By synonyms** — try alternative phrasings (e.g. "evaluate" instead of "assess", "fairness" instead of "bias")

Fetch the top results and for each: read the title, description, intended results, and context.

## Step 3: Assess Similarity

For each candidate practice found, assess similarity across three dimensions:

| Dimension | Strong match | Weak match |
|-----------|-------------|------------|
| **Verb/action** | Same or synonymous action verb | Different action family |
| **Object/scope** | Same governance object | Adjacent but different object |
| **Context** | Same project/enterprise context | Different context |

Classify each candidate as:
- **EXACT MATCH** — this OP covers the described activity; recommend reusing it
- **STRONG MATCH** — this OP is very similar; recommend reviewing before creating a new one
- **RELATED** — this OP is in the same domain but has a different scope; note for context
- **WEAK** — mentioned for completeness, unlikely to be a match

## Step 4: Check for Gaps

If no strong match is found, confirm:
- Which IMR category the new OP would fall under
- Whether the gap is genuine (new governance activity not yet in the registry) or a search coverage issue

## Output Format

```
## Similar OPs Found for: "[Activity Description]"

### Search Summary
- **Governance domain:** [Value Groups searched]
- **Keywords used:** [list of search terms tried]
- **Candidates evaluated:** [number]

---

### Results

#### EXACT / STRONG MATCH: [OP Key] — [OP Title]
**Context:** Project / Enterprise
**Why it matches:** [2–3 sentences explaining the similarity]
**Description:** [OP description excerpt]
**Recommendation:** Reuse this OP. Do not create a new one.

#### RELATED: [OP Key] — [OP Title]
**Context:** Project / Enterprise
**How it differs:** [1–2 sentences on scope difference]

---

### Conclusion
[One of:]
- "A suitable OP already exists — recommend reusing [OP Key]."
- "No exact match found. The closest is [OP Key] but it differs in [specific way]. A new OP may be warranted in IMR category [IMRC name]."
- "No existing OP covers this activity. Recommend creating a new OP — use /op-from-activity or /op-from-title."
```

Now search for existing OPs matching the described activity.
