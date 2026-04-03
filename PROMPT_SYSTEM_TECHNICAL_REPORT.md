# Federal Proposal Generation — Prompt System Technical Report

**Project:** Semi-Synthetic Federal Proposal Generation for ML Training
**Sector Focus:** Health & Human Services (HHS)
**Files Covered:** `claude_chat_prompt.md`, `capability_json_prompt.md`, `sectors/hhs_prompt.md`
**Date:** 2026-04-03

---

## 1. Overview

This system is a structured prompt framework for generating complete, semi-synthetic federal government proposals using Claude AI chat. The primary purpose is to produce high-quality training data for an ML model — proposals that are structurally complete, grounded in real contract data, and honest about what is known versus unknown.

The system is built around three core files that work together in a defined workflow:

| File | Role |
|---|---|
| `claude_chat_prompt.md` | Base prompt — drives full proposal generation for any federal contract |
| `capability_json_prompt.md` | Company intelligence export — captures awardee research into a structured JSON |
| `sectors/hhs_prompt.md` | HHS sector-specific prompt — extends the base prompt with Health & Human Services domain knowledge |

The output of each run is three artifacts per contract:
- `[notice_id].docx` — the complete proposal document
- `[notice_id]_review.md` — drafter's audit (writing plan, coverage check, grounding integrity check)
- `[notice_id]_gaps.json` — structured gap and confidence data for programmatic use

---

## 2. Workflow

The workflow runs in six sequential steps in Claude chat before the main prompt is pasted:

```
Step 1  — Upload solicitation documents + SAM.gov CSV row
Step 2  — Document read and summary; identify sub-agency (HHS only)
Step 3  — Compliance requirements and evaluation criteria grid
Step 3.5 — Web research: capability statement, GSA vehicles, key personnel,
            USASpending + NIH Reporter award history (with source URLs)
Step 4  — Confirm section list (8 base + any additional required by docs)
Step 5  — Paste the main prompt → generates full proposal
Step 6  — Rate the proposal
```

Step 3.5 also triggers a separate prompt (`capability_json_prompt.md`) to export all company intelligence found into a reusable JSON file.

---

## 3. Base Prompt — `claude_chat_prompt.md`

### 3.1 Purpose

The base prompt is the core of the system. It is sector-agnostic and applies to any federal contract. It drives a three-phase workflow: plan → write → audit.

### 3.2 Phase 1 — Planning

Before writing any section, the LLM produces a **Proposal Writing Plan** that maps every evaluation subfactor and SOW task to the correct section. This prevents sections from being written in isolation without knowing what they need to score on.

The plan includes:
- Contract summary (one sentence)
- Top differentiators (2–3, grounded in documents)
- Compliance Gate table (pass/fail requirements by section)
- Evaluation Criteria table (scored subfactors by section)
- Section plan table (one row per section with eval subfactors, SOW tasks, and required details)
- Win Themes block (2–3 themes with evidence and reinforcement instructions)

### 3.3 Phase 2 — Writing

Eight base sections are always generated regardless of what the solicitation explicitly scores:

| # | Section |
|---|---|
| Cover | Cover Page |
| 1.0 | Executive Summary |
| 2.0 | Technical Approach |
| 3.0 | Management Plan |
| 4.0 | Past Performance |
| 5.0 | Timeline & Milestones |
| 6.0 | Risk Mitigation |
| 7.0 | Compliance |
| 8.0 | Pricing Rationale |

Each section has specific writing rules. The Technical Approach enforces operational specificity ("exactly how will this work get done") over generic compliance statements. Past Performance rules prevent fabrication of contract numbers, amounts, or dates.

**Phase 2B** scans all attached documents for submittable forms (pricing schedules, past performance worksheets, SF-1449, etc.) and fills every field that can be derived from the documents.

### 3.4 Placeholder System

A key design decision for ML training data quality: the prompt does not allow fields to be silently omitted. When required data is not available in the source documents, the LLM writes a structured inline placeholder directly in the proposal body.

**Format:** `_______________ [INSERT: description of what is needed]`

This ensures every proposal is structurally complete — every required field appears, either filled or as a labelled blank. The `[INSERT: ...]` tag is programmatically identifiable for downstream processing.

A **Placeholder Catalogue** in the prompt standardises labels for common missing fields:

| Field | Placeholder |
|---|---|
| Program Manager name | `_______________ [INSERT: Program Manager full name]` |
| TIN | `_______________ [INSERT: Taxpayer Identification Number (TIN)]` |
| Per-unit rate | `_______________ [INSERT: rate per unit/circuit/line item, USD]` |
| F&A / overhead rate | `_______________ [INSERT: current F&A / overhead rate]` |
| CPARS rating | `_______________ [INSERT: CPARS rating — e.g. Exceptional / Very Good]` |
| Key personnel cert | `_______________ [INSERT: technician name and credential number]` |

### 3.5 Phase 3 — Quality Checks

Two automated quality checks run after all sections are written:

**Check B — Evaluation Coverage**
Verifies every scored subfactor from the writing plan was addressed in the correct section. Outputs a coverage table with Addressed / Partial / Missing status per section and an overall score (X of Y subfactors).

**Check C — Grounding Integrity**
Scans every factual claim for three failure modes: invented (not in source documents), contaminated (from wrong document), or wrong (contradicts confirmed facts). Each claim is traced to its source document and section. Web-researched claims are traced to their source URL.

### 3.6 Web Research Integration

Web research (Step 3.5) is treated as a valid source on equal footing with attached documents, with one requirement: source URLs must be recorded in citations.

Global Rule 1 was updated to reflect this: facts may come from attached documents or from web research completed in Step 3.5, with source URL. The Grounding Integrity Check table includes a `Source URL` column for web-sourced claims.

### 3.7 Confidence Scoring

The `_gaps.json` output includes a two-dimensional confidence score:

| Dimension | What it measures | Ideal for ML training |
|---|---|---|
| `structural_completeness` | Are all sections and fields present (filled or placeholder)? 10 = nothing omitted | Primary signal — a complete proposal with placeholders scores 10 |
| `data_substantiation` | How much filled content is backed by real source documents? | Secondary signal — shows how much human review is needed |

This replaces the prior single score which penalised proposals for having placeholders. A structurally complete proposal with some placeholders is now correctly recognised as high quality training data.

### 3.8 Gaps JSON Schema

The `_gaps.json` file captures per-section structured data for programmatic use:

```
section_gaps → per section:
  generic_statements    — sentences written generically due to missing data
  placeholders          — each placeholder inserted, with exact placeholder_text
  assumptions_made      — inferences not in source documents
  citations             — every factual claim with source_document, source_section, source_url

submittable_forms → per form:
  fields_filled         — confirmed fields
  placeholders          — unfilled fields with placeholder_text
  fill_completeness     — fraction filled

overall_confidence:
  structural_completeness (score + placeholder_count)
  data_substantiation (score + limiting_factor)
```

### 3.9 Global Rules

20 global rules apply to every section. Key rules:

| Rule | What it enforces |
|---|---|
| 1 | No hallucination — every fact from documents or web research with URL |
| 2 | No generic compliance filler |
| 4 | Every section addresses its assigned eval subfactors |
| 7 | Placeholders for missing data — never silent omission |
| 10 | Write to content, not word count |
| 11 | Win themes reinforced in every relevant section |
| 12 | Cross-reference, never repeat |
| 14 | Evidence over assertion |

---

## 4. Capability JSON Prompt — `capability_json_prompt.md`

### 4.1 Purpose

After Step 3.5 web research completes, this prompt converts all found company intelligence into a structured, reusable JSON file. The output is `{company_name}_capability.json` — a company profile that can be referenced in future proposals without re-running research.

### 4.2 Schema Structure

The JSON captures eight categories of company intelligence:

| Field | Contents |
|---|---|
| Identity fields | `company_name`, `uei`, `cage`, `registered_address`, `business_type`, `size_standard` |
| `services` | Service categories and line items |
| `scale_stats` | Key operational metrics (e.g. number of mitigations, network size) |
| `personnel` | Named individuals with title, bio summary, and source URL |
| `awards` | Every contract — award ID, agency, value, period, NAICS, set-aside, source URL |
| `contract_modifications` | Every mod — parent PIID, mod number, date, amount change, cumulative obligation |
| `contract_vehicles` | GSA schedules, IDIQs, BPAs with SIN/pool and source URL |
| `certifications` | Each cert with confirmed/not found status and source URL |
| `competitive_notes` | Sole-source patterns, agency concentration, incumbent contracts, exploitable gaps |
| `sources` | Every URL accessed including those that returned nothing |
| `research_completeness` | Boolean checklist: capability statement found, award history retrieved, etc. |

### 4.3 Design Rules

- **Only uses Step 3.5 findings** — no new searches, no inference
- **One object per award, one per modification** — no summarising
- **Every URL in `sources`** — including blocked and login-gated ones
- **`research_completeness` is honest** — if a source was not checked, it is `false`
- **Output is file only** — no prose, no inline JSON in chat

---

## 5. HHS Sector Prompt — `sectors/hhs_prompt.md`

### 5.1 Purpose

The HHS sector prompt extends the base prompt with Health & Human Services domain knowledge. It inherits the full base prompt and adds a sector context block that runs before Phase 1. It is designed to be used in place of the base prompt for any HHS contract.

### 5.2 Contract-Type Classifier

The most important addition. Before applying any HHS-specific guidance, the LLM identifies the contract type and applies only the relevant subset:

| Type | Description | Guidance applied |
|---|---|---|
| **Type A** | Infrastructure / Supply / Commodity | HHSAR clauses, technical standards, supply/delivery operations framing |
| **Type B** | Professional Services / IT / Program Management | HHSAR clauses, Section 508, QASP, key personnel scrutiny, salary cap (non-FFP only) |
| **Type C** | Clinical Research / Medical Countermeasures | All HHS guidance: human subjects, HIPAA, GCP, IRB, publication approval, salary cap, research methodology framing |

This gates guidance that would otherwise appear for every contract regardless of relevance — preventing human subjects language from appearing in a fiber infrastructure proposal, for example.

### 5.3 Agency Landscape

A reference table covers all 11 HHS operating divisions with their mission focus and what they look for in proposals. HHS evaluators are frequently subject matter experts (clinicians, scientists, public health professionals) not just contracting officers — the prompt instructs writing to domain fluency appropriate to the specific sub-agency.

An additional note distinguishes **contracting activity** (the office issuing the solicitation, e.g. DAAPPO) from **requiring office** (the program office whose mission is being served). The prompt instructs writing to the requiring office's priorities, not generic contracting language.

### 5.4 HHSAR Clause Set

A reference table of the most common HHSAR 352.xxx clauses with what each requires:

| Clause | Requirement |
|---|---|
| 352.237-75 | Key personnel — named individuals, CO approval required for replacements |
| 352.270-2 | Inspection and acceptance rights |
| 352.223-70 | Safety and health practices |
| 352.224-70 | Privacy Act — PII protection |
| 352.227-70 | Publications and publicity — government review rights |
| 352.233-71 | Litigation and claims — dispute resolution |

The prompt enforces that only clauses found in the solicitation documents are referenced — never added because the contract is with HHS.

### 5.5 Common HHS Requirements (Conditional Application)

Each requirement includes a conditional rule for when to apply it and when not to:

- **Salary cap ($221,900):** Apply on cost-reimbursable, T&M, and labor-hour contracts only. On FFP, compensation is embedded in fixed pricing — the cap is not a line-item compliance issue and should not be referenced.
- **Human subject protections:** Type C contracts only (clinical research, patient data, medical countermeasures). Not for IT, supply, or infrastructure.
- **HIPAA:** Only when individually identifiable health information is in scope.
- **Section 508:** Full application for Type B/C with IT deliverables. Documentation-only for Type A.
- **QASP:** Reference when SOW explicitly describes government surveillance of contractor performance.
- **Key personnel (HHSAR 352.237-75):** When clause appears in solicitation — named individuals required, with placeholders for unknowns.

### 5.6 Technical Approach — Supply vs. Services Branch

The Technical Approach section contains an explicit branch:

- **Supply/commodity (Type A):** Write as an operations document — delivery cadence, cold chain compliance, quality release, ordering mechanics. Evaluators are procurement and technical staff, not peer reviewers.
- **Services/research (Type B/C):** Write with domain fluency to the specific work. Demonstrate operational understanding of how the work gets done.

### 5.7 Past Performance Enhancements

Two enhancements beyond the base prompt:

**Incumbent anchor:** When the awardee previously held a contract for the same scope at the same facility, that contract is treated as Reference 1 and leads the section by name — framed as identical-scope performance, not just a relevant reference.

**LPTA vs. best-value distinction:** On LPTA, past performance is a risk threshold to clear — write to demonstrate acceptable risk, not impressive history. On best-value, past performance is a competitive discriminator — write to differentiate with outcomes, CPARS ratings, and scope complexity. The incumbent anchor is especially powerful on LPTA because identical prior scope essentially guarantees an acceptable risk determination.

### 5.8 HHS-Specific Past Performance Sources

The capability search step (Step 3.5) is extended to include:
- **NIH Reporter** (reporter.nih.gov) — research grants and contracts
- **HRSA Awards** (data.hrsa.gov) — health workforce and community health contracts
- **CMS Contractor Directory** — Medicare/Medicaid systems contractors
- **FDA Contractor List** — regulated industry prior relationships

### 5.9 HHS-Specific Global Rules (15–20)

| Rule | What it enforces |
|---|---|
| 15 | Write to evaluator's domain expertise — HHS evaluators are subject matter experts |
| 16 | Salary cap on cost-reimbursable/T&M/labor-hour only; not on FFP |
| 17 | Human subject protections only when work involves clinical research or patient data |
| 18 | HIPAA only when individually identifiable health data is in scope |
| 19 | HHSAR clauses must come from the solicitation documents — never added by assumption |
| 20 | Past performance must reflect HHS-relevant experience; note CPARS availability |

---

## 6. Design Principles

### 6.1 Document-Driven, Not Sector-Hardcoded

The base prompt derives all content from attached solicitation documents and verified web research. Hardcoding sector-specific facts would produce wrong output for contracts that don't share those facts. The HHS sector prompt adds domain knowledge layered on top of this principle — it tells the LLM what to look for and how to frame it, not what the answer is.

### 6.2 Structural Completeness Over Data Completeness

For ML training purposes, a structurally complete proposal with placeholders is more valuable than a proposal that omits fields it cannot fill. The placeholder system and the `structural_completeness` score are both designed to enforce this principle.

### 6.3 Conditional Guidance Reduces Noise

A recurring design issue was guidance that applied to some contracts but not others appearing in every run, requiring the LLM to consciously suppress it. The contract-type classifier (HHS prompt), salary cap contract-type condition, and human subjects/HIPAA conditional rules all address this by gating guidance behind explicit conditions rather than listing it and relying on suppression.

### 6.4 Grounding Integrity as a First-Class Output

Every factual claim in the proposal is traced to a source document, section, and URL (for web-sourced claims). This is not a post-hoc review — it is built into the output structure via the `_gaps.json` citations schema and the Grounding Integrity Check. This makes the training data auditable at the claim level.

---

## 7. File Summary

| File | Lines (approx.) | Key additions vs. base |
|---|---|---|
| `claude_chat_prompt.md` | ~600 | Placeholder system, dual confidence score, web research citations, source URLs |
| `capability_json_prompt.md` | ~147 | Standalone company intelligence schema |
| `sectors/hhs_prompt.md` | ~600 | Contract-type classifier, agency landscape, HHSAR clause set, conditional guidance, LPTA/best-value distinction, incumbent anchor, 6 HHS global rules |

---

## 8. Contracts Tested

| Notice ID | Sub-Agency | NAICS | Type |
|---|---|---|---|
| 75F40121R00001 | FDA | 541519 — Other Computer Related Services | Type A (Infrastructure) |
| 75H70724Q00049 | IHS | 334516 — Analytical Laboratory Instruments | Type A (Supply) |
| 2023-BARDA-DCOHRE | BARDA | 541714 — R&D Biotechnology | Type C (Clinical Research) |
