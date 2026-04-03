# Proposal Comparison Report
## Generic Prompt vs. HHS Sector Prompt
**Contract:** 75F40121R00001 — Dark Fiber Circuits, FDA
**V1:** Generated using `claude_chat_prompt.md` (base/generic prompt)
**V2:** Generated using `sectors/hhs_prompt.md` (HHS sector-specific prompt)

---

## Summary Scorecard

| Dimension | V1 (Generic) | V2 (HHS Sector) |
|---|---|---|
| Sections generated | 8 | 8 |
| Subsections | 18 | 22 |
| Contract-type classification applied | No | Yes — Type A declared |
| Inapplicable HHS guidance suppressed | No | Yes (HIPAA, human subjects, salary cap) |
| HHSAR clause citations in body | 1 | 4 |
| LPTA framing in Past Performance | No | Yes |
| Incumbent contract named prominently | No | Yes (HHSF223201610001C) |
| Evaluator audience addressed | Generic | FDA IT staff / procurement officers |
| Salary cap correctly excluded (FFP) | No | Yes — explicitly excluded with reason |
| Risk 5 specificity | Generic (option year risk) | Operational (credential expiration at secured sites) |

---

## Section-by-Section Comparison

### Cover Page

**V1**
Standard fields. Missing TIN and CO contact details.

**V2**
Added TIN placeholder explicitly. Added CO name and email directly on cover page: `Natasha Abbington, Contracting Officer — natasha.abbington@fda.hhs.gov`. This came from the solicitation document; V1 missed it.

**Why V2 is better:** Cover page is the first thing an evaluator opens. Having the correct CO name signals this proposal was prepared for this specific solicitation, not templated.

---

### 1.0 Executive Summary

**V1 — key paragraph:**
> "We are uniquely qualified for this requirement because dark fiber lease-and-maintenance for federal civilian agencies in the DC Metro corridor is our core operational domain."

**V2 — added paragraph not in V1:**
> "FDA's evaluators have deep technical familiarity with the DC MAN — they operate it. Generic proposals that describe fiber capability without demonstrating knowledge of this specific network's geometry and SLA architecture will be immediately apparent to this evaluation panel."

**Why V2 is better:** V2's Executive Summary directly acknowledges who is reading it — IT infrastructure staff who run the network being proposed on. This came from HHS Global Rule 15 ("write to the evaluator's expertise"). V1 made no distinction between evaluator types. The added paragraph also doubles as a win theme reinforcement, which is strategically stronger.

---

### 2.0 Technical Approach

**V1 structure:** 5 subsections (2.1–2.5), flat
**V2 structure:** 3 main subsections with sub-subsections (2.1.1, 2.1.2, 2.1.3, 2.2.1, 2.2.2, 2.2.3)

**V1 opening:**
> "Our technical approach is organized around the two task areas defined in the SOW..."

**V2 opening:**
> "CONTRACT TYPE APPLIED: Type A — Infrastructure / Physical Commodity. This section is written as an operations document: delivery cadence, maintenance mechanics, standards conformance, repair workflows, and NOC response architecture. No clinical research framing, scientific methodology, or public health evaluator tone is applied."

**Key difference — V2 Section 2.2.2 (Four-Hour Response):**

V1 describes the response protocol generically. V2 adds two operational specifics V1 did not include:

> "For access delays: we maintain a directory of after-hours security contacts and facility manager phone numbers for all nine FDA MAN building complexes. This contact list is reviewed quarterly and updated whenever we receive notice of personnel changes."

> "For underground conduit damage: if initial field assessment indicates a dig-in or conduit collapse requiring excavation, we immediately notify the FDA COR and Contracting Officer by email within 30 minutes of that determination..."

**Why V2 is better:** The contract-type classifier (Type A) prevented V2 from importing research/clinical framing into an infrastructure section. V2's sub-subsection structure is also easier to cross-reference against SOW sections in evaluation. The access credential and conduit damage scenarios are specific operational risks of this network that V1 treated generically.

---

### 3.0 Management Plan

**V1 — key personnel section:**
> "Program Manager: [placeholder]. The Program Manager is the primary point of contact for the FDA COR..."

**V2 — same section, with addition:**
> "HHSAR 352.237-75 (Key Personnel) applies to this contract. The Program Manager and Lead Field Engineer identified above cannot be replaced without Contracting Officer approval. Replacement candidates must be submitted in writing with equivalent qualifications at least 30 days in advance."

**V2 also adds qualification placeholders V1 did not have:**
- `_______________ [INSERT: Program Manager relevant certifications, e.g. PMP, telecom credentials]`
- `_______________ [INSERT: Lead Field Engineer credentials, e.g. BICSI RCDD, Telcordia fiber certification]`

**Why V2 is better:** HHSAR 352.237-75 is a real clause in this solicitation. V1 described key personnel without citing the clause or its replacement requirements. V2 both cites the clause and explains the compliance obligation — which is what the evaluation panel will check against the clause text. V2 also surfaces the certification placeholders that V1 left structurally absent.

---

### 4.0 Past Performance

**V1 opening:**
> "The following past performance references demonstrate our organizational experience providing dark fiber lease and maintenance services..."

**V2 opening — entirely different framing:**
> "This is an LPTA contract. The past performance evaluation is a risk threshold assessment — the FDA evaluator is asking whether this offeror poses acceptable performance risk, not ranking the impressive breadth of our history. Two references are required. Both are provided below."

**V1 section header for the incumbent reference:**
> "4.1 Reference 1 — Federal Civilian Agency, Dark Fiber Lease and Maintenance"

**V2 section header:**
> "4.1 Reference 1 — FDA Dark Fiber Circuits (Incumbent Performance)"

**V2 also names the predecessor contract explicitly:**
> "The USASpending award history confirms contract HHSF223201610001C (Food and Drug Administration, 2015–2021) as the direct predecessor to this solicitation..."

**V1 conclusion subsection title:** "4.3 Relevance to This Requirement"
**V2 conclusion subsection title:** "4.3 Risk Determination"

**Why V2 is better:** Three structural improvements from the HHS prompt:

1. **LPTA framing** — V1 wrote past performance as a scored factor. On LPTA, it is a threshold. The evaluator's question is different, so the answer should be different.
2. **Incumbent anchor** — V2 leads with the predecessor contract by name and labels it explicitly as incumbent performance. V1 buried it as a generic reference.
3. **"Risk Determination" not "Relevance"** — V2's terminology matches the evaluation methodology. An LPTA evaluator is determining risk, not scoring relevance.

---

### 6.0 Risk Mitigation

Both versions identify 5 risks. Four are substantially similar. Risk 5 differs significantly:

**V1 — Risk 5:**
> "Risk: The Government may decline to exercise one or more option years... any infrastructure investment or staffing expansion made in anticipation of option exercise would not be recoverable."

**V2 — Risk 5:**
> "Risk: All nine FDA MAN building sites are federally secured facilities. Field technicians require active access credentials to enter for repairs. If a credential expires before renewal is completed, a technician may be blocked from entering a facility during an active repair event — directly threatening four-hour SLA compliance."

**Why V2 is better:** V1's Risk 5 is a contract structure risk (option non-exercise) that is irrelevant to how evaluators score technical risk. It describes something the government controls, not something the contractor mitigates. V2's Risk 5 is an actual operational failure mode specific to federal secured facilities — a real SLA threat that demonstrates knowledge of what it means to work on a government campus. This came from applying the Type A operations-framing discipline.

---

### 7.0 Compliance

**V1 opening:** Goes directly into Section 508 subsection.

**V2 opening:**
> "CONTRACT TYPE APPLIED: Type A — Infrastructure/Physical Commodity. Human subject protections (45 CFR 46, 21 CFR Parts 50/54/56), HIPAA, GCP, IRB, FedRAMP, CMMC, FISMA, and salary cap language are not applicable to this contract and are deliberately excluded from this section."

**V1 subsections:** 7.1–7.6 (6 subsections)
**V2 subsections:** 7.1–7.7 (7 subsections — adds 7.6 Invoice Submission)

**V2 adds invoice submission detail V1 lacked:**
> "Invoices will be submitted to FDAVendorPaymentsTeam@fda.hhs.gov on company letterhead in Adobe Acrobat PDF format, with a courtesy copy to the COR. Each invoice will include all elements required by FAR 52.212-4..."

**Why V2 is better:** V1's compliance section could have applied HIPAA or human subject language by mistake — the generic prompt gave no guidance on when to suppress it. V2 explicitly flags what does not apply and why, which is more credible to an evaluator than just omitting it. The invoice submission addition is a real compliance requirement from the solicitation that V1 missed.

---

### 8.0 Pricing Rationale

**V1 — no note on salary cap:**
Discusses per-circuit pricing, discount, and price reasonableness. No mention of salary cap applicability.

**V2 — opens with explicit exclusion:**
> "NOTE: Salary cap language is deliberately excluded from this section. This is a Firm Fixed Price contract (confirmed in SAM.gov award record: contractType = FIRM FIXED PRICE). On FFP contracts, HHS does not fund individual salaries — it funds a fixed deliverable. The Executive Level II salary cap ($221,900) is not a line-item compliance requirement here."

**Both versions name predecessor contract for price comparability. V2 is more precise:**

V1: `"the predecessor contract for this same work (HHSF223201610001C, 2015–2021) was $8,387,188"`

V2: `"USASpending award data provides two direct comparators: (1) the predecessor FDA dark fiber contract (HHSF223201610001C)..."`

**Why V2 is better:** V1 generated a pricing section that could have included salary cap language (which would have been wrong for FFP and potentially confusing to evaluators). V2 proactively resolves this by explaining the exclusion. The explicit note demonstrates contractual literacy — an evaluator reading V2 knows the drafter understood the contract type.

---

## What the HHS Sector Prompt Added That Changed Real Output

| HHS Prompt Feature | Where it changed V2 output |
|---|---|
| Contract-type classifier (Type A) | Technical Approach opener; Compliance opener; suppressed HIPAA/human subjects |
| Global Rule 15 (write to evaluator expertise) | Executive Summary added evaluator-awareness paragraph |
| HHSAR clause list (352.237-75) | Management Plan — clause named, replacement rules stated |
| LPTA past performance distinction | Past Performance — reframed as risk threshold, not scored factor |
| Incumbent anchor instruction | Past Performance 4.1 header renamed; predecessor contract named in opener |
| Salary cap FFP exclusion rule | Pricing Rationale — explicit exclusion note with reason |
| Risk 5 ops-framing from Type A | Risk Mitigation — option year risk replaced with credential expiration risk |

## What the Generic Prompt Got Right (Unchanged in V2)

The base prompt's core rules held up well and V2 did not need to change:
- Section 508 correctly scoped to documentation-only for physical infrastructure
- SOW technical standards cited precisely (ITU-T G.652, GR-20-CORE, 21 CFR 1040)
- Milestone dates grounded in USASpending transaction history
- No fabricated contract numbers or dollar amounts
- Placeholder system applied consistently across both versions
- Win themes threaded through all sections
- Cross-referencing between sections (not repeating)
