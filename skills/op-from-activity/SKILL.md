---
name: op-from-activity
description: Generate complete OPs (title, description, IRs) from a governance activity description
argument-hint: <description of governance activity>
---

# Generate Complete OP(s) from Activity Description

You are an expert in data and AI governance. Given a description of a governance activity, generate one or more complete OPs (Operational Practices) following the Prodago guidelines.

## Input
The user described this governance activity: $ARGUMENTS

## Analysis Steps

1. **Understand the activity** — What governance need does it address?
2. **Determine scope** — Is this one OP or should it be broken into multiple OPs?
3. **Generate complete OP(s)** — Each with title, description, and intended results

## Guidelines for OP Titles

**Structure:** [Action Verb] + [Object] + [Optional: Context/Qualifier]

**Recommended Action Verbs:**
| Category | Verbs |
|----------|-------|
| Creation | Establish, Create, Develop, Define, Design |
| Documentation | Document, Record, Prepare, Compile |
| Analysis | Identify, Assess, Analyze, Evaluate, Review, Audit |
| Implementation | Implement, Execute, Conduct, Perform, Apply |
| Communication | Communicate, Publish, Disclose, Report, Present |
| Control | Monitor, Track, Manage, Control, Maintain |
| Approval | Approve, Validate, Verify, Obtain approval for, Attest |
| Planning | Plan, Schedule, Map, Align |
| Classification | Classify, Categorize, Prioritize |
| Training | Train, Educate, Onboard |

**Rules:**
- Start with an action verb in imperative form (Establish, not Establishing)
- Be specific and actionable
- Must include an object (verb alone is NOT valid)
- Keep concise: 5–12 words
- AVOID vague outcome-based titles (Bad: "Ensure data quality" / Good: "Implement data validation controls")

## Guidelines for Descriptions

**Structure:** Exactly TWO sentences:
1. Gerund phrase (-ing form matching the title verb) describing WHAT
2. "This includes..." followed by 3–4 specific sub-tasks

**Rules:**
- Sub-tasks must be small, granular tasks — NOT separate OPs
- Use present participle (-ing) in sub-tasks
- Formal, objective, third-person tone (no "you", "we", "I")

## Guidelines for Intended Results (IRs)

**Purpose:** Expected outcomes, benefits, goals — the "why" behind the activity

**Rules:**
- Each IR is one short sentence ending with a passive verb
- Do NOT restate the title — focus on VALUE/BENEFIT
- Let the activity's value determine how many IRs are needed (not a fixed count)
- Consider: accountability, standardization, risk reduction, compliance, clarity, efficiency, auditability

**Passive verb endings:** is established, is enabled, is ensured, is reduced, are standardized, are aligned, is maintained

## Output Format

For each OP, provide:

```
---
## OP [number]: [Title]

**Title:** [Action verb + object, 5–12 words]

**Description:** [Two sentences: gerund phrase + "This includes..." with sub-tasks]

**Intended Results:**
- [IR 1]
- [IR 2]
- [IR 3 if warranted]
---
```

## Example

**Input:** "We need to make sure our AI systems are being used ethically and that we have proper oversight of how they make decisions"

**Output:**
```
---
## OP 1: Establish an AI ethics review process

**Title:** Establish an AI ethics review process

**Description:** Establishing a structured process for reviewing the ethical implications of AI systems before deployment. This includes defining review criteria, assembling review teams, and documenting review outcomes.

**Intended Results:**
- Ethical risks of AI systems are identified before deployment.
- Accountability for AI ethics decisions is established.
- Alignment with organizational values and ethical standards is ensured.

---
## OP 2: Document AI decision-making logic and criteria

**Title:** Document AI decision-making logic and criteria

**Description:** Documenting how AI systems arrive at decisions to ensure transparency and explainability. This includes capturing input variables, decision rules, weighting factors, and output interpretations.

**Intended Results:**
- Transparency in AI decision-making processes is ensured.
- Auditability of AI system behavior is enabled.
- Stakeholder understanding of AI outputs is improved.

---
## OP 3: Implement AI system monitoring and oversight controls

**Title:** Implement AI system monitoring and oversight controls

**Description:** Implementing controls to continuously monitor AI system behavior and performance. This includes defining monitoring metrics, setting alert thresholds, and establishing escalation procedures.

**Intended Results:**
- Anomalous AI behavior is detected and addressed promptly.
- Ongoing compliance with ethical guidelines is ensured.
- Continuous improvement of AI systems is enabled.
---
```

## Validation Checklist (Self-Critique Before Output)

Before providing your final output, validate EACH OP against this checklist. Revise and re-validate if any check fails.

### Title Validation
- [ ] Starts with an action verb in imperative form
- [ ] Contains an object (not just a verb)
- [ ] Is specific and actionable (not vague like "Ensure" or "Maintain")
- [ ] Is 5–12 words

### Description Validation
- [ ] Exactly TWO sentences
- [ ] First sentence starts with gerund (-ing) matching the title verb
- [ ] Second sentence starts with "This includes"
- [ ] Sub-tasks are granular (NOT separate OPs)
- [ ] Sub-tasks use present participle (-ing form)
- [ ] Formal, objective, third-person tone (no "you", "we", "I")

### Intended Results Validation
- [ ] Each IR is ONE short sentence
- [ ] Each IR ends with a passive verb (is established, is ensured, etc.)
- [ ] IRs focus on VALUE/BENEFIT, not just restating the title
- [ ] IRs are distinct from each other (no redundancy)
- [ ] Number of IRs fits the activity — not forced to a fixed count

### Common Mistakes to Avoid
- ❌ IR that restates the title: "AI ethics review process is established"
- ✅ IR that shows value: "Ethical risks of AI systems are identified before deployment"
- ❌ Sub-task that's an OP: "establishing an ethics committee"
- ✅ Granular sub-task: "defining review criteria"

### Multi-OP Validation
- [ ] Each OP is distinct (no overlap in scope)
- [ ] OPs are appropriately granular (not too broad, not too narrow)
- [ ] Together, OPs fully address the described activity

## Process

1. **Analyze** the activity description
2. **Generate** initial OP(s) with title, description, and IRs
3. **Validate** each OP against the checklist above (internally)
4. **Revise** if any checks fail
5. **Output** only the final validated version(s)

Now analyze the provided activity description and generate the appropriate OP(s), validating before output.
