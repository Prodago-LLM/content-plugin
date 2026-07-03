---
name: op-from-title
description: Generate OP description and intended results from an OP title
argument-hint: <OP title>
---

# Generate OP Description and IRs from Title

You are an expert in data and AI governance. Given an OP (Operational Practice) title, generate a description and intended results following the Prodago guidelines.

## Input
The user provided this OP title: $ARGUMENTS

## Title Validation

Before generating, validate the input title:
- Must start with an **imperative action verb** (Establish, Define, Implement, etc.)
- Must include an **object** (verb alone is invalid — "Assess" is incomplete; "Assess data quality risks" is complete)
- Must be **specific and actionable** — avoid vague outcome-based titles (BAD: "Ensure data quality"; GOOD: "Implement data validation controls")
- Aim for **5–12 words**

If the title doesn't follow these rules, flag the issue and suggest an improved title before generating.

## Guidelines for Description

**Structure:** Exactly TWO sentences:
1. Gerund phrase (-ing form matching the title verb) describing WHAT the activity accomplishes
2. "This includes..." followed by specific sub-tasks

**Template:**
```
[Gerund phrase matching title verb] [what is accomplished]. This includes [sub-task 1], [sub-task 2], and [sub-task 3].
```

**Rules:**
- Start with a gerund that matches the title verb (Establish → Establishing)
- Sub-tasks must be small, granular tasks — NOT separate OPs (if a sub-task sounds like it could be its own OP, it's too big)
- Use present participle (-ing) in sub-tasks
- Formal, objective, third-person tone (no "you", "we", "I")

## Guidelines for Intended Results (IRs)

**Purpose:** Describe the expected outcomes, benefits, or goals — the "why" behind the activity.

**Rules:**
- Each IR is **one short sentence** ending with a **passive verb**
- Do NOT restate the title — focus on VALUE/BENEFIT achieved
- Do NOT force a fixed number of IRs — let the activity's value determine how many are needed
- Consider multiple value perspectives (see Thinking Framework below)

**Thinking Framework — ask: "Why do we perform this activity? What value does it provide?"**

| Value Type | Example IR Pattern |
|------------|-------------------|
| Accountability | Accountability for X is established. |
| Standardization | X practices are standardized across the organization. |
| Risk Reduction | Risks related to X are identified and mitigated. |
| Compliance | Compliance with X requirements is ensured/demonstrated. |
| Clarity | Clear understanding of X is established among stakeholders. |
| Efficiency | Consistency and efficiency in X processes is enabled. |
| Auditability | Audit trail for X decisions/actions is maintained. |

**Passive verb endings to use:**
is established / are established, is enabled / are enabled, is ensured / are ensured,
is reduced / are reduced, is standardized / are standardized, is clarified / are clarified,
are aligned, is maintained, is achieved, is improved

## Output Format

```
**Title:** [repeat the input title]

**Description:** [Two sentences following the structure above]

**Intended Results:**
- [IR 1 — focusing on a key benefit/value]
- [IR 2 — focusing on another benefit/value]
- [... more if the activity warrants additional distinct benefits]
```

## Example

**Input:** Establish an AI governance committee

**Output:**
```
**Title:** Establish an AI governance committee

**Description:** Establishing a committee responsible for overseeing and guiding the governance of AI within the organization. This includes selecting committee members, defining their roles and responsibilities, and scheduling regular meetings.

**Intended Results:**
- Accountability for AI governance decisions is established.
- AI-related risks are systematically identified and addressed.
- Alignment between AI initiatives and organizational policies is ensured.
```

## Validation Checklist (Self-Critique Before Output)

### Title Validation
- [ ] Starts with an imperative action verb
- [ ] Includes an object (not just a verb)
- [ ] Specific and actionable (not a vague outcome)
- [ ] 5–12 words

### Description Validation
- [ ] Exactly TWO sentences
- [ ] First sentence starts with gerund (-ing) matching the title verb
- [ ] Second sentence starts with "This includes"
- [ ] Sub-tasks are granular (NOT separate OPs)
- [ ] Sub-tasks use present participle (-ing form)
- [ ] Formal, objective, third-person tone (no "you", "we", "I")

### Intended Results Validation
- [ ] Each IR is ONE short sentence
- [ ] Each IR ends with a passive verb
- [ ] IRs focus on VALUE/BENEFIT, not restating the title
- [ ] IRs are distinct from each other (no redundancy)
- [ ] Number of IRs fits the activity — NOT forced to a fixed count

### Common Mistakes to Avoid
- ❌ IR that restates the title: "Guidelines for metadata management are defined"
- ✅ IR that shows value: "Consistency in metadata management practices across the organization is ensured"
- ❌ Sub-task that's an OP: "defining a metadata policy"
- ✅ Granular sub-task: "specifying metadata attributes to be captured"

## Process

1. **Generate** initial description and IRs
2. **Validate** against the checklist above (internally)
3. **Revise** if any checks fail
4. **Output** only the final validated version

Now generate the description and IRs for the provided title, validating before output.
