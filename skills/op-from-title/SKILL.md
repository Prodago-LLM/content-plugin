---
name: op-from-title
description: Generate a complete OP from a title — description, intended results, context, full IMR/OVP placement, PP phase, and AM mapping
argument-hint: <OP title>
---

# Generate a Complete OP from a Title

You are an expert in data and AI governance. Given an OP (Operational Practice) title, generate the full OP record — content **and** registry placement — following the Prodago guidelines, using live data from the PrimeRise CE registry via MCP.

## Input
The user provided this OP title: $ARGUMENTS

## What This Skill Produces

| # | Output | How it's determined |
|---|--------|---------------------|
| 1 | Description | Reasoning only — Prodago writing standard (Step 2) |
| 2 | Intended Results | Reasoning only — Prodago writing standard (Step 3) |
| 3 | Context (project / enterprise) | Reasoning only — decision table (Step 4) |
| 4 | IMR / Data Management OVP placement — all 4 levels, code + title | MCP: `get_imr_structure`, `list_ovps` (Step 5) |
| 5 | PP phase (exactly one) | MCP: `get_pp_structure` (Step 6) |
| 6 | AM mapping (1 accountable + 1–3 responsible) | MCP: `search_practices`, `get_practice_context`, `list_accountability_areas`, `get_am_structure` (Step 7) |

Steps 1–4 are reasoning-only. Steps 5–7 **require live MCP calls against the PrimeRise CE registry** — never invent a code.

---

## Adaptation Notice

This skill originates from an SME's local workflow, which queries a private Excel-derived export (`imr/imr_index.json`, `reference/OVT-0009.json`, `reference/am_prodago.json`, `reference/project_phases_pp0004.json`) with Python. This plugin has no filesystem access to those files — it only has the `primerise-ce` MCP tools, which query the same underlying Prodago registry live (Postgres, via the PrimeRise app). Four consequences of that switch:

1. **IMR and the "Data Management OVP" are the same node, not two files to reconcile.** The registry represents IMR as OVP code 9 (confirmed directly in the PrimeRise MCP server source: *"IMR is represented as OVP with code=9 per Prodago's standard schema"*). The SME's two separate local exports could drift out of sync with each other (hence their "16 of 580 entries have no matching OVP path" caveat) — that scenario doesn't exist here, because there's only one live source. Steps 5 and 6 from the SME's version are therefore merged into a single lookup below.
2. **No MCP tool keyword-searches the IMR hierarchy or resolves a code to its full path in one call.** `get_imr_structure` only returns Value Groups (level 1). Getting Category/Type/leaf levels requires `list_ovps`, which returns the full nested hierarchy for *every* OVP in the org as indented Markdown text — you locate the right branch and walk the indentation yourself (Step 5 spells out exactly how).
3. **No MCP tool lists "which OPs already sit in this IMR bucket"** (the SME's sibling-context step for AM mapping). Substitute: keyword-search existing OPs with `search_practices`, then read each candidate's accountability via `get_practice_context` (Step 7.1).
4. **Leadership Area names are not exposed by any current MCP tool — only codes.** (The underlying schema has the name field; the tool query just doesn't fetch it.) Functional Area names and Accountability/Context-level names are available and should be used; Leadership Area appears by code only.

---

## MCP Tools Used

| Tool | Format | Purpose |
|------|--------|---------|
| `get_imr_structure` | JSON-in-text | Value Groups (level 1) only, for the IMR/OVP-9 tree — Step 5.1 |
| `list_ovps` | Markdown | Full nested hierarchy (all levels) for **every** OVP in the org — Step 5.2–5.3. **Large response** — jump straight to the `## OVP-9` section and the Value Group block you selected in 5.1; don't reason over unrelated OVPs. |
| `get_pp_structure` (`pp_code: "PP0004"`) | JSON-in-text | The PP0004 phase set and its phase details — Step 6 |
| `search_practices`, `get_practice_context` | Markdown | Best-effort sibling-OP context for AM mapping — Step 7.1 |
| `list_accountability_areas` | Markdown | Context → Leadership (code) → Functional (code) hierarchy — Step 7.2 |
| `get_am_structure` | JSON-in-text | Flat list of Functional Areas with names, for keyword search — Step 7.2 |

Note: `get_imr_structure`, `get_pp_structure`, and `get_am_structure` all declare a required `org_id` parameter in their schema, but the server ignores it and always scopes to your authenticated org — pass any non-empty string.

---

## Step 0: Design Principles

Three principles govern everything below (Prodago's OP design standard — apply directly, no lookup needed):

| Principle | Rule |
|-----------|------|
| **Generality for reuse** | OPs are generic building blocks reused across COs. Strip CO-specific language. If you write "per [regulation]" or "as required by [CO]", that belongs in a **specification**, not the OP. |
| **Title self-sufficiency** | The title alone must convey the activity, with no CO context needed to understand it. |
| **Activities, not tasks** | An OP is an activity (an APQC-style *Activity*), not a single executable step. "Conduct privacy impact assessments" ✅ / "Complete the PIA questionnaire" ❌. The sub-tasks in the description are the tasks. |

---

## Step 1: Title Validation

Validate the input title before generating anything:

- Starts with an **imperative action verb** (Establish, Define, Implement, Conduct, Monitor…)
- Includes an **object** — a verb alone is invalid ("Assess" ❌ / "Assess data quality risks" ✅)
- **Specific and actionable** — not a vague outcome ("Ensure data quality" ❌ / "Implement data validation controls" ✅)
- **5–12 words**
- Reads as an **activity**, not a task (Step 0)

If the title fails any check, flag the issue and propose an improved title **before** generating the rest.

---

## Step 2: Description

**Structure:** Exactly TWO sentences.

```
[Gerund phrase matching the title verb] [what is accomplished]. This includes [sub-task 1], [sub-task 2], ..., and [sub-task N].
```

**Rules:**

1. **Open with a gerund that matches the title verb.** Three acceptable forms:
   - *Literal*: Establish → Establishing · Define → Defining · Review → Reviewing
   - *Synonym*: Analyze → Examining · Establish → Creating
   - *Decomposition*: Monitor → Tracking and reviewing

   The match must be obvious to a reader. Decompositions must **fully cover** the title verb — never narrow, broaden, or drift from it.

2. **Exactly two sentences** — no more, no less.

3. **Sub-tasks must not be OPs.** If a sub-task sounds like it could stand as its own OP, it is too big — break it down.
   - ❌ "This includes establishing a committee, defining policies, and implementing controls" (three separate OPs)
   - ✅ "This includes selecting committee members, defining their roles and responsibilities, and scheduling regular meetings"

4. **Sub-task count follows natural granularity — typically 2–5. Do NOT default to three.**
   - Labor-intensive OPs (establishing a committee, running a full impact assessment) decompose into 4–5 distinct steps — use them.
   - Light OPs (logging an approval, recording a metric) may only warrant 2 — do **not** pad with a filler step.
   - Never compress a rich OP into three slots by merging legitimate steps.
   - Healthy range 2 ≤ N ≤ 5. More than 5 → the OP is doing too much. Exactly 1 → the description is just restating the title.

5. **Present participle (-ing)** used consistently across sub-tasks.

6. **Formal, objective, third person** — no "you", "we", "I".

---

## Step 3: Intended Results (IRs)

**Purpose:** the expected outcomes, benefits, or value — the "why" behind the activity.

**Rules:**
- Each IR is **one short sentence** ending with a **passive verb**
- Do **not** restate the title — focus on VALUE/BENEFIT achieved
- Do **not** force a fixed count. Simple activities may warrant 1; complex activities with several distinct benefits 3+. Let the activity decide.
- Cover **distinct** value perspectives — no two IRs saying the same thing

**Thinking framework — ask: "Why do we perform this activity? What value does it provide?"**

| Value Type | Example IR Pattern |
|------------|-------------------|
| Accountability | Accountability for X is established. |
| Standardization | X practices are standardized across the organization. |
| Risk Reduction | Risks related to X are identified and mitigated. |
| Compliance | Compliance with X requirements is ensured/demonstrated. |
| Clarity | Clear understanding of X is established among stakeholders. |
| Efficiency | Consistency and efficiency in X processes is enabled. |
| Auditability | Audit trail for X decisions/actions is maintained. |

**Passive verb endings:** is/are established · is/are enabled · is/are ensured · is/are reduced · is/are standardized · is/are clarified · are aligned · is maintained · is achieved · is improved

---

## Step 4: Context (Project vs Enterprise)

Determined by **scope** and **recurrence**. This drives the PP mapping in Step 6, so settle it first.

| Context | OP In Context | Scope | Recurrence |
|---------|---------------|-------|------------|
| **Project** | True | A specific project, product, or data asset | Performed every time there is a new project |
| **Enterprise** | False | Organization-wide; policies, standards, frameworks, governance structures | Performed once (or periodically) for the organization |

**Decision table** — walk in order, stop at the first Yes:

| Question | If Yes |
|----------|--------|
| Performed once for the whole organization? | Enterprise |
| Defines policies, standards, or frameworks? | Enterprise |
| Establishes committees or governance structures? | Enterprise |
| Performed every time there is a new project? | Project |
| Performed per project, product, or data asset? | Project |
| Applies to a specific initiative or deliverable? | Project |
| *(none of the above)* | Enterprise |

---

## Step 5: IMR / Data Management OVP Placement (All Four Levels)

Report the **complete path** — Value Group → Category → Type → IMR leaf — each with **code and title**. This single lookup covers both "IMR placement" and the "Data Management OVP bucket" — in the live registry they are the same node (see Adaptation Notice, point 1).

**5.1 — Narrow to a Value Group.** Call `get_imr_structure` (small, clean JSON — just the level-1 Value Groups). Pick the Value Group matching the OP's primary domain:

| OP Domain | Value Group |
|-----------|-------------|
| Project management | VG10 (Projects) |
| Data governance, metadata, architecture | VG11 (Data) |
| AI, ML, analytics, metrics | VG12 (Products) |
| Cloud governance | VG13 (Cloud) |
| Risk management (AI, model, privacy, security) | VG14 (Risks) |
| Data quality | VG2 (Quality) |
| Privacy | VG3 (Privacy) |
| Security | VG4 (Security) |
| Ethics, fairness, explainability | VG5 (Ethics) |

(Codes above are indicative — always use the actual codes/names `get_imr_structure` returns for this org, not these labels.)

**5.2 — Find candidate leaf entries.** Call `list_ovps`. This returns every OVP in the org as Markdown, each OVP's detail rows indented by level (`"  ".repeat(level - 1)`), i.e.:

```
## OVP-9: <IMR / Data Management name>
  - **VG##**: <Value Group name>            <- level 1, 0 indent
    - **IMRC##**: <Category name>           <- level 2, 2-space indent
      - **IMRT###**: <Type name>            <- level 3, 4-space indent
        - **IMR###**: <leaf name>           <- level 4, 6-space indent
```

Locate the `## OVP-9` section, then within it the block for the Value Group chosen in 5.1 (its 0-indent line and everything indented beneath it, up to the next 0-indent line). Scan the deepest-indented (leaf) lines in that block for keyword matches against the OP's title/domain. Try synonyms if the first pass finds nothing.

**5.3 — Record the full path.** Once a leaf is selected, walk upward through the indentation to its parent Type, Category, and Value Group lines — record code + name at all four levels.

**Validation:**
- [ ] VG matches the OP's primary domain
- [ ] Category (IMRC) is the most specific applicable one
- [ ] No better-fit category exists in an adjacent VG
- [ ] All four levels reported with **both** code and title
- [ ] Codes copied verbatim from the tool output — none invented

**If no leaf entry fits**, do not force-fit. Report `Not Found` and prompt the user — creating a new IMR/OVP node is a rare, user-owned decision, not something to guess at.

---

## Step 6: PP Mapping (Exactly One Phase)

Call `get_pp_structure` with `pp_code: "PP0004"`. It returns `{ id, code, name, details: [{ id, code, order, name }] }` for the phase set. **Codes come back as raw integers, not `PPD####` strings** — zero-pad to 4 digits and prefix `PPD` yourself (e.g. `details[].code = 32` → `PPD0032`; the phase set's own `code = 4` → `PP0004`).

**Rules:**
- Exactly **one** phase per OP. Never multiple.
- **Enterprise OPs → Organisation Requirements (PPD9045)**, always.
- Project OPs → the phase where the activity **most often / most probably** occurs, not merely where it *can* occur.
- **Tiebreaker:** when two phases are roughly equally probable, choose the **earliest** applicable one.

**Phase is driven by the OBJECT the OP acts upon, not the verb.** "Assess business requirements" → Requirements Analysis; "Assess model test results" → Testing and Evaluation; "Assess production performance" → Monitoring and Maintenance.

| Object of Action | Phase | Key |
|------------------|-------|-----|
| *(enterprise OPs — any object)* | Organisation Requirements | PPD9045 |
| Project scope, feasibility, initial risks | Initiation | PPD0032 |
| Plans, strategy, resource allocation, schedule, methodology | Planning | PPD0033 |
| Business needs, stakeholder/functional requirements, use cases | Requirements Analysis | PPD0034 |
| Architecture, schema, data model, system design, interfaces | Design | PPD0035 |
| Environment, tools, infrastructure, configuration, access | Preparation | PPD0036 |
| Code, pipelines, models, transformations (building them) | Development | PPD0037 |
| Test results, quality metrics, accuracy, benchmarks | Testing and Evaluation | PPD0038 |
| Stakeholder approval, business acceptance, compliance sign-off | Validation and Sign-off | PPD0039 |
| Production deployment, release, go-live, cutover | Deployment / Operation | PPD0040 |
| Users, teams, knowledge transfer, skills | Training | PPD0041 |
| Production health, drift, alerts, incidents, patches | Monitoring and Maintenance | PPD0042 |
| Scale, capacity, geographic expansion, new use cases | Upscaling | PPD0043 |
| Legacy systems, end-of-life, decommissioning, archival | Retiring | PPD0044 |
| Activities genuinely recurring across multiple phases | Ongoing | PPD0045 |

Confirm each of these codes against what `get_pp_structure` actually returned for this org before reporting — the table above is reference guidance, not a substitute for the live call.

**Two distinctions that are commonly confused:**

| Pair | How to tell them apart |
|------|------------------------|
| Planning vs Preparation | Planning = strategy/approach/methodology. Preparation = technical setup, environments, tooling. |
| Testing vs Validation | Testing = technical team checking correctness. Validation = stakeholders granting business acceptance. |
| Ongoing vs Monitoring | Ongoing = recurs **across phases**. Monitoring = happens **in production**, post-deployment. |

Do not reach for "Ongoing" as a default — most OPs belong to a single specific phase.

---

## Step 7: AM Mapping (AMCO001)

**Cardinality:** exactly **1 accountable** + **1 to 3 responsible**.

| Role | Count | Focus |
|------|-------|-------|
| **Accountable** | Exactly 1 | Oversight, guidance, resource allocation, alignment, escalation point |
| **Responsible** | 1–3 | Execution, design, implementation, delivery |

**Assign Functional Areas, never Leadership Areas.** Leadership areas ("Office of CDO") are containers used to navigate; functional areas ("Data Quality Management") do the work and are what gets mapped.

**7.1 — Gather sibling context (best-effort substitute).** No MCP tool enumerates "OPs in this IMR bucket" directly (see Adaptation Notice, point 3). Instead:
- Call `search_practices` with 2–3 keywords from the OP title/domain (`limit: 10` or so).
- For up to ~5 of the most relevant results, strip the `OP-` prefix from the code shown and call `get_practice_context(code)`.
- Read each one's `## Accountability` section for existing Accountable/Responsible functional-area assignments.
- Optionally prefer candidates whose `[Enterprise]`/`[Project]` scope tag (shown by `search_practices`) matches the context you determined in Step 4.

Read these for **context and awareness** — which functions are active in this domain. They legitimately differ from OP to OP (different execution focus, different oversight authority, different scope). **Do not blindly copy a match's mapping**; assess this OP on its own merits.

**7.2 — Search functional areas by keyword.** Call `get_am_structure` — a flat `{ functional_areas: [{ id, code, name }] }` list for the whole org. Filter by keyword against `name` (case-insensitive substring):

| Keywords | Leadership Area (for orientation only — see below) |
|----------|-----------------------------------------------------|
| privacy, consent, GDPR, PII, personal data, data subject | Office of CPO |
| access control, encryption, breach, threat, authentication | Office of CISO |
| model, algorithm, ML, training, inference, prediction | Office of CAO |
| quality, accuracy, completeness, validation, profiling | Office of CDO |
| retention, archival, lifecycle, disposal, purge | Office of CDO |
| lineage, metadata, catalog, dictionary | Office of CDO |
| risk, impact, assessment, mitigation, control | Office of CRO |
| compliance, regulation, audit, certification, attestation | Office of CLO / CCO |
| training, awareness, education, competency | Office of CHRO |
| ethics, fairness, bias, transparency, explainability | Office of Chief Ethics Officer |
| architecture, integration, infrastructure, systems | Office of CIO |
| budget, cost, procurement, vendor | Office of CFO |

If `get_am_structure`'s name-only search doesn't surface a clear candidate, call `list_accountability_areas` — it groups the same functional-area codes under Leadership Areas and Accountability/Context areas (with practice counts), useful to confirm which domain a candidate functional-area code sits under. **Leadership Area names are not available from any tool — only codes** (see Adaptation Notice, point 4); report Leadership by code if you need to mention it, and lead with the Functional Area's resolved name.

**Assign 2–3 responsibles** only when the activity genuinely spans domains — cross-functional work, shared execution, specialist + generalist, or design + implementation split.

---

## Output Format

```
**Title:** [the input title, or the corrected title if the input failed validation]

**Description:** [two sentences]

**Intended Results:**
- [IR 1]
- [IR 2]
- [... as many as the activity's value warrants]

**Context:** Project / Enterprise  (OP In Context = True / False)

**IMR / Data Management OVP Placement (OVP-9):**
| Level | Code | Title |
|-------|------|-------|
| Value Group | [VG##] | [title] |
| Category | [IMRC##] | [title] |
| Type | [IMRT###] | [title] |
| IMR / OVP Leaf | [IMR###] | [title] |

**PP Phase (PP0004):** [PPD####] | [phase name]

**AM Mapping (AMCO001):**
| Role | Functional Area Code | Functional Area |
|------|----------------------|------------------|
| Accountable | [code] | [name] |
| Responsible | [code] | [name] |
| Responsible | [code] | [name] *(if applicable)* |

**Placement rationale:** [2-3 sentences — why this IMR/OVP leaf, why this phase, why this accountable]
```

---

## Worked Example

This example illustrates the target shape only — **all codes below are illustrative**, not guaranteed to exist in any specific org's registry. Never report a code from this example without first confirming it via the live MCP tools for the org you're actually working in.

**Input:** `Establish an AI governance committee`

**Output:**

```
**Title:** Establish an AI governance committee

**Description:** Creating a committee responsible for overseeing and guiding the governance of AI within the organization. This includes selecting committee members, defining their roles and responsibilities, and establishing regular meetings to discuss and make decisions on AI governance matters.

**Intended Results:**
- Accountability for AI governance decisions is established across the organization
- Clear decision-making authority for AI-related matters is ensured

**Context:** Enterprise  (OP In Context = False)

**IMR / Data Management OVP Placement (OVP-9):**
| Level | Code | Title |
|-------|------|-------|
| Value Group | VG12 | Products |
| Category | IMRC40 | AI |
| Type | IMRT115 | AI Governance |
| IMR / OVP Leaf | IMR386 | AI Accountabilities |

**PP Phase (PP0004):** PPD9045 | Organisation Requirements

**AM Mapping (AMCO001):**
| Role | Functional Area Code | Functional Area |
|------|----------------------|------------------|
| Accountable | AMCO001-AA0098 | CAO Function |
| Responsible | AMCO001-AA0066 | Project management (corporate) |

**Placement rationale:** The committee governs AI decision-making, placing it under AI Governance → AI Accountabilities rather than a risk or ethics bucket. It is established once for the organization, so context is enterprise and the phase is Organisation Requirements. The CAO Function holds oversight authority for AI, while corporate project management executes the standing-up of the committee.
```

Two things this example demonstrates:
- **"Establish" → "Creating"** is a permitted *synonym* gerund under Step 2 Rule 1 — the match is obvious and fully covers the title verb.
- **Two IRs, not three.** The activity has two distinct benefits; padding to three would have forced a redundant IR.

---

## Validation Checklist (Self-Critique Before Output)

Run every check. If any fails, revise and re-validate until all pass.

### Title
- [ ] Imperative action verb
- [ ] Has an object (not a bare verb)
- [ ] Specific and actionable, not a vague outcome
- [ ] 5–12 words
- [ ] Reads as an activity, not a task
- [ ] Contains no CO-specific language

### Description
- [ ] Exactly TWO sentences
- [ ] Opens with a gerund matching the title verb (literal, synonym, or full decomposition)
- [ ] Second sentence starts with "This includes"
- [ ] Sub-tasks are granular — none could stand as its own OP
- [ ] Sub-task count reflects natural granularity (2–5), **not** defaulted to three
- [ ] Present participle used consistently in sub-tasks
- [ ] Formal, objective, third person

### Intended Results
- [ ] Each IR is ONE short sentence
- [ ] Each ends with a passive verb
- [ ] Focus on VALUE/BENEFIT, not a restatement of the title
- [ ] IRs are distinct — no two express the same value
- [ ] Count fits the activity, **not** forced to a fixed number

### Context
- [ ] Determined via the decision table, not assumed
- [ ] Consistent with the PP phase (enterprise ⇒ PPD9045)

### IMR / Data Management OVP
- [ ] All four levels reported, each with code **and** title
- [ ] Codes copied verbatim from `get_imr_structure`/`list_ovps` output — none invented
- [ ] Category is the most specific applicable one
- [ ] Checked adjacent Value Groups for a better fit
- [ ] If no leaf fits: reported as `Not Found` — no code invented

### PP
- [ ] Exactly one phase
- [ ] Enterprise OP ⇒ PPD9045; project OP ⇒ a lifecycle phase
- [ ] Phase chosen from the OBJECT acted upon, not the verb
- [ ] Code confirmed against the live `get_pp_structure` response, not assumed from the reference table
- [ ] "Ongoing" used only for genuinely cross-phase activities
- [ ] "Monitoring and Maintenance" used only for post-deployment work

### AM
- [ ] Exactly 1 accountable
- [ ] Between 1 and 3 responsible
- [ ] All assignments are **Functional Areas**, not Leadership Areas
- [ ] Codes/names verified against `get_am_structure` — none invented
- [ ] Sibling OPs consulted via `search_practices` + `get_practice_context` for context — but not blindly copied
- [ ] Accountable has genuine oversight authority; responsible can genuinely execute
- [ ] Oversight and execution roles are distinct

### Common Mistakes to Avoid
- ❌ IR that restates the title: "Guidelines for metadata management are defined"
- ✅ IR that shows value: "Consistency in metadata management practices across the organization is ensured"
- ❌ Sub-task that is itself an OP: "defining a metadata policy"
- ✅ Granular sub-task: "specifying metadata attributes to be captured"
- ❌ Forcing exactly 3 IRs or exactly 3 sub-tasks regardless of the activity
- ❌ Inventing an IMR, PPD, or AMCO001 code that wasn't returned by an MCP tool call
- ❌ Reporting only Value Group → Category instead of the full four-level path
- ❌ Assigning a Leadership Area ("Office of CDO") instead of a Functional Area
- ❌ Copying a keyword-matched OP's AM mapping without assessing this OP on its own merits
- ❌ Treating the worked example's codes as real for the current org

---

## Process

1. **Validate** the title; propose a correction first if it fails
2. **Generate** description and IRs
3. **Determine** context
4. **Call** `get_imr_structure`, then `list_ovps` — select the IMR/OVP-9 leaf, resolve the full four-level path
5. **Call** `get_pp_structure("PP0004")` — select the PP phase
6. **Call** `search_practices`/`get_practice_context` for sibling context, then `get_am_structure`/`list_accountability_areas` — assign the AM mapping
7. **Validate** everything against the checklist (internally)
8. **Revise** if any check fails
9. **Output** only the final validated version

## Related Skills

| Need | Skill |
|------|-------|
| You have an activity description, not a title | `/op-from-activity` |
| Map a CO statement to OPs end to end | `/map-co-statement` |

**Assumed already done:** the redundancy check — confirming no existing OP covers this activity — happens *before* this skill is invoked (e.g. via `/find-similar-ops`). Do not re-run it, and do not caveat the output with it.

Now generate the complete OP for the provided title, validating before output.
