---
name: clarity-gate
description: Pre-ingestion verification for epistemic quality in RAG systems. Use when reviewing documents before they enter knowledge bases, checking if claims could be misinterpreted as facts, or validating that hypotheses are clearly marked. Triggers on "clarity gate", "check for hallucination risks", "can an LLM read this safely", "review for equivocation", "verify document clarity", "pre-ingestion check".
---

# Clarity Gate v1.4

**Purpose:** Pre-ingestion verification system that enforces epistemic quality before documents enter RAG knowledge bases.

**Core Question:** "If another LLM reads this document, will it mistake assumptions for facts?"

**Core Principle:** *"Detection finds what is; enforcement ensures what should be. In practice: find the missing uncertainty markers before they become confident hallucinations."*

**Prior Art:** This skill builds on proven patterns from enterprise gates (Adlib, pharma QMS) and detection tools (UnScientify, HedgeHog). Our contribution is open-source epistemic enforcement. See [docs/PRIOR_ART.md](docs/PRIOR_ART.md).

**Origin:** Part of [Stream Coding](https://github.com/frmoretto/stream-coding) methodology. Clarity Gate is the mandatory checkpoint before code generation. This standalone version enables verification without the full methodology.

---

## The Key Distinction

Existing tools like UnScientify and HedgeHog **detect** uncertainty markers already present in text ("Is uncertainty expressed?").

Clarity Gate **enforces** their presence where epistemically required ("Should uncertainty be expressed but isn't?").

| Tool Type | Question | Example |
|-----------|----------|---------|
| **Detection** | "Does this text contain hedges?" | UnScientify finds "may", "possibly" |
| **Enforcement** | "Should this claim be hedged but isn't?" | Clarity Gate flags "Revenue will be $50M" |

---

## Critical Limitation

> **Clarity Gate verifies FORM, not TRUTH.**
>
> This skill checks whether claims are properly marked as uncertain--it cannot verify if claims are actually true. 
>
> **Risk:** An LLM can hallucinate facts INTO a document, then "pass" Clarity Gate by adding source markers to false claims.
>
> **Solution:** HITL (Human-In-The-Loop) verification is **MANDATORY** before declaring PASS.

---

## When to Use

- Before ingesting documents into RAG systems
- Before sharing documents with other AI systems
- After writing specifications, state docs, or methodology descriptions
- When a document contains projections, estimates, or hypotheses
- Before publishing claims that haven't been validated
- When handing off documentation between LLM sessions

---

## The 7 Verification Points

### Epistemic Checks (Core Focus: Points 1-4)

**1. HYPOTHESIS vs FACT LABELING**
Every claim must be clearly marked as validated or hypothetical.

**Check:** Are projections, estimates, and assumptions explicitly labeled?

| Fails | Passes |
|-------|--------|
| "Our architecture outperforms competitors" | "Our architecture outperforms competitors [benchmark data in Table 3]" |
| "The model achieves 40% improvement" | "The model achieves 40% improvement [measured on dataset X]" |

**Fix:** Add markers: "PROJECTED:", "HYPOTHESIS:", "UNTESTED:", "(estimated)", "~", "?"

---

**2. UNCERTAINTY MARKER ENFORCEMENT**
Forward-looking statements require qualifiers.

**Check:** Do predictions and projections have explicit uncertainty markers?

| Fails | Passes |
|-------|--------|
| "Revenue will be $50M by Q4" | "Revenue is **projected** to be $50M by Q4" |
| "The feature will reduce churn" | "The feature is **expected** to reduce churn" |

**Fix:** Add "projected", "estimated", "expected", "designed to", "intended to"

---

**3. ASSUMPTION VISIBILITY**
Implicit assumptions that affect interpretation must be explicit.

**Check:** Are hidden conditions and constraints stated?

| Fails | Passes |
|-------|--------|
| "The system scales linearly" | "The system scales linearly [assuming <1000 concurrent users]" |
| "Response time is 50ms" | "Response time is 50ms [under standard load conditions]" |

**Fix:** Add bracketed conditions: "[assuming X]", "[under conditions Y]", "[when Z]"

---

**4. AUTHORITATIVE-LOOKING UNVALIDATED DATA**
Tables with specific percentages and checkmarks look like measured data.

**Check:** Could a reader mistake this table/chart for empirical results?

**Red flag:** 
| Approach | Accuracy | Success Rate |
|----------|----------|--------------|
| Method A | 89% | 66% |
| Method B | 95% | 100% |

**Fix:** Add "(guess)", "(est.)", "?" to numbers. Change checkmarks to question marks for unvalidated items. Add explicit warning: "PROJECTED VALUES - NOT MEASURED"

---

### Data Quality Checks (Complementary: Points 5-7)

**5. DATA CONSISTENCY**
Scan for conflicting numbers, dates, or facts within the document.

**Check:** Does the same metric appear with different values?

**Red flag:** "500 users" in one section, "750 users" in another

**Fix:** Reconcile conflicts or explicitly note the discrepancy with explanation.

---

**6. IMPLICIT CAUSATION**
Claims that imply causation without evidence.

**Check:** Does the document claim X causes Y without validation?

**Red flag:** "Shorter prompts improve response quality" (plausible but unproven)

**Fix:** Reframe as hypothesis: "Shorter prompts MAY improve response quality (hypothesis, not validated)"

---

**7. FUTURE STATE AS PRESENT**
Describing planned/hoped outcomes as if already achieved.

**Check:** Are future goals written in present tense?

**Red flag:** "The system processes 10,000 requests per second" (when it hasn't been built)

**Fix:** Use future/conditional: "The system is DESIGNED TO process..." or "TARGET: 10,000 rps"

---

## The Verification Hierarchy

The key insight: **not all claims can be verified the same way.**

```
Claim Extracted --> Does Source of Truth Exist?
                           |
           +---------------+---------------+
           YES                             NO
           |                               |
   Tier 1: Automated              Tier 2: HITL
   Consistency & Verification     Intelligent Routing
           |                               |
   PASS / BLOCK                   APPROVE / REJECT
```

### Tier 1: Automated Consistency & Verification

**A. Internal Consistency (Ready Now)**

Checks for contradictions *within* a document--no external systems required.

| Check Type | Example |
|------------|---------|
| Figure vs. Text | Figure shows B=0.33, text claims B=0.73 |
| Abstract vs. Body | Abstract claims "40% improvement," body shows 28% |
| Table vs. Prose | Table lists 5 features, text references 7 |
| Numerical consistency | Revenue stated as $47M in one section, $49M in another |

This is the **core capability**--solvable, generic, and valuable. A document with conflicting claims becomes a "trusted source of confusion"--the model confidently reports one value, unaware they conflict.

**B. External Verification (Extension Interface)**

For claims that *can* be verified against structured sources:

| Claim Type | Source | Implementation |
|------------|--------|----------------|
| "Q3 revenue was $47M" | Financial system | User provides connector |
| "Feature deployed Oct 15" | Git commits | User provides API |
| "Customer count: 2,847" | CRM | User provides query |

**Note:** External verification requires bespoke integration for each data source. Clarity Gate provides the *interface* for verification hooks; users implement connectors for their specific systems.

---

### Tier 2: HITL Fallback (Intelligent Routing) -- MANDATORY

The value isn't having humans review data--every team does that. The value is **intelligent routing**: the system detects *which* specific claims need human review, sparing humans from reviewing safe ones.

*Example: A 50-claim document might have 48 pass automated checks. The system routes only 2 for human review, reducing manual effort by ~96%.*

---

**Step 0: Request Source of Truth**

Before extracting claims, ask:

> "Do you have a Source of Truth document for this project?
> (e.g., project state record, verified metrics, status tracker)
>
> If yes, please share it -- I'll use it to verify claims.
> If no, I'll present claims for manual verification."

---

**Why HITL exists:** Points 1-7 verify that claims are MARKED correctly. They cannot verify if claims are TRUE. An LLM may confidently write false information derived from:
- Misremembered conversations
- Incorrect pattern-matching from context
- Confusing proposals/plans with completed work
- Assuming demo = deployment, or planned = achieved

**Process:**

1. **Extract claims** - List every significant factual assertion:
   - Metrics (users, revenue, percentages)
   - Events (deployments, launches, partnerships)
   - Outcomes (results, achievements, validations)
   - Comparisons (X is better than Y, X achieved Z)

2. **Present to human** in this format:
   ```
   ## HITL Verification Required
   
   Before I can declare PASS, please confirm these claims are TRUE:
   
   | # | Claim | Source in Doc | Human Confirms |
   |---|-------|---------------|----------------|
   | 1 | [specific claim] | [section/line] | [ ] True / [ ] False |
   | 2 | [specific claim] | [section/line] | [ ] True / [ ] False |
   | 3 | [specific claim] | [section/line] | [ ] True / [ ] False |
   
   Please respond with corrections for any false claims.
   ```

3. **Wait for human response** - Do NOT proceed until human confirms

4. **Apply corrections** - Fix any claims the human identifies as false

**Red flags that REQUIRE HITL:**
- Case studies or customer examples
- Deployment/production claims
- User counts or adoption metrics
- Revenue or cost figures
- "Zero defects" or "100%" claims
- Partnership or client names
- Before/after comparisons

**The human's job is specific:**
1. Provide the Source of Truth that was missed, OR
2. Add appropriate epistemic markers ([PROJECTION], [HYPOTHESIS], [UNVERIFIED]), OR
3. Reject the claim entirely

This creates an **audit trail**: Document X, Claim Y, verified against Source Z on Date W by Person P.

---

**Source of Truth Template (Adaptable)**

If the human doesn't have a Source of Truth, suggest creating one:

```
# [Subject] - Source of Truth
Last Updated: [date]
Owner: [name/team]
Status: [current state]

## Verified Data
| Item | Value | Context | Verified Date |
|------|-------|---------|---------------|
| [what] | [measured value] | [conditions, source, method] | [when] |

## Status Tracker
| Item | Status | Notes |
|------|--------|-------|
| [what] | [None / Planned / In Progress / Completed / N/A] | [details] |

## Stakeholders / Relationships
| Entity | Relationship | Status |
|--------|--------------|--------|
| [who] | [type] | [current state] |

## Sources
| Claim | Source Type | Reference |
|-------|-------------|-----------|
| [claim] | Internal | [internal doc] |
| [claim] | External | [citation] |
| [claim] | None | Unverified |

## Open Items
| ID | Priority | Description | Status |
|----|----------|-------------|--------|
```

**Key principles:**
- Explicit status markers: None, Planned, In Progress, Completed, Verified, N/A
- Context for every value: How/where/when was it measured?
- Date everything
- Separate Internal from External sources

---

**Source Types:**

| Type | Definition | Example |
|------|------------|---------|
| **Internal** | Verified state of this project/context | Metrics, status records, team data |
| **External** | Published research, third-party data | Academic papers, industry reports, official statistics |
| **None** | Claim without supporting source | Requires HITL verification |

---

## Quick Scan Checklist

Run through document looking for these patterns:

| Pattern | Action |
|---------|--------|
| Specific percentages (89%, 73%, etc.) | Add source or mark as estimate |
| Comparison tables | Add "PROJECTED" header + uncertainty markers |
| "Achieves", "delivers", "provides" | Check if validated; if not, use "designed to", "intended to" |
| Checkmarks | Verify these are actually confirmed |
| "100%" anything | Almost always needs qualification |
| Time/cost savings claims | Mark as projected unless measured |
| "The model recognizes/understands/knows" | Mark as hypothesis about LLM behavior |
| "Always", "never", "guarantees" | Check if absolute claim is warranted |
| **Case studies / customer names** | **HITL REQUIRED - verify with human** |
| **Production deployments** | **HITL REQUIRED - verify with human** |
| **Measured outcomes** | **HITL REQUIRED - verify with human** |

---

## Output Format

After running Clarity Gate, report:

```
## Clarity Gate Results

**Issues Found:** [number]

### Critical (will cause hallucination)
- [issue + location + fix]

### Warning (could cause equivocation)  
- [issue + location + fix]

### Passed (Points 1-7)
- [what was already clear]

---

## HITL Verification Required

Before declaring PASS, please confirm these claims are TRUE:

| # | Claim | Source in Doc | Human Confirms |
|---|-------|---------------|----------------|
| 1 | [claim] | [location] | [ ] True / [ ] False |
| 2 | [claim] | [location] | [ ] True / [ ] False |

---

**Would you like me to produce an annotated version of this document with the fixes applied?**

(An annotated document improves how mid-tier LLMs handle the content.)

---

**Verdict:** PENDING HITL CONFIRMATION
```

**Only after human confirms can you update to:**

```
**Verdict:** PASS (HITL confirmed [date])
```

**If user requests annotation:**

Produce the complete document with all fixes applied inline:
- Uncertainty markers added
- Assumptions made visible
- Projections labeled
- Unverified claims marked

This creates a **Clarity-Gated Document (CGD)** ready for RAG ingestion or handoff to other LLM systems.

---

## Severity Levels

| Level | Definition | Action |
|-------|------------|--------|
| **CRITICAL** | LLM will likely treat hypothesis as fact | Must fix before use |
| **WARNING** | LLM might misinterpret | Should fix |
| **HITL REQUIRED** | Factual claim needs human verification | Cannot pass without confirmation |
| **PASS** | Clearly marked, no ambiguity, HITL confirmed | No action needed |

---

## What This Skill Does NOT Do

- Does not classify document types (use Stream Coding for that)
- Does not restructure documents 
- Does not add deep links or references
- Does not evaluate writing quality
- **Does not check factual accuracy autonomously** (requires HITL for factual claims)

**This skill verifies:** 
1. (Points 1-4) Epistemic quality: Are claims properly qualified?
2. (Points 5-7) Data quality: Is the document internally consistent?
3. (HITL) Truth verification: Has a human confirmed factual claims?

---

## Example Fixes

**Before:** "The new architecture reduces latency by 40%"
**After:** "The new architecture is PROJECTED to reduce latency by ~40% (UNTESTED)"

**Before:** "Method B is the superior approach"
**After:** "HYPOTHESIS: Method B may be more efficient (not yet validated through comparative testing)"

**Before:** 
| Method | Success Rate |
|--------|--------------|
| Ours | 100% |

**After:**
| Method | Success Rate (ESTIMATED) |
|--------|--------------------------|
| Ours | ~100%? (assumes ideal conditions) |

**Before:** "Users prefer the new interface"
**After:** "HYPOTHESIS: Users may prefer the new interface (based on 3 informal interviews, no formal study)"

**Before:** "Deployed to 500+ employees at Acme Corp with zero data leaks"
**After (if HITL reveals this was only a demo):** "Demo presented to Acme Corp. NO production deployment. Zero data leaks is a design goal, not a measured outcome."

---

## HITL Failure Case Study

**What happened:**

1. LLM wrote document about a project
2. Included "Enterprise deployment: 500+ employees, zero PII leaks, 6 months production"
3. Ran Clarity Gate points 1-7
4. Added "(client-reported)" marker to claims
5. Declared PASS

**The problem:**
- The client only saw a demo--there was NO production deployment
- The LLM misinterpreted past conversations
- Adding "(client-reported)" made a FALSE claim look MORE credible
- Clarity Gate verified the FORM but not the TRUTH

**What HITL would have caught:**

```
## HITL Verification Required

| # | Claim | Source in Doc | Human Confirms |
|---|-------|---------------|----------------|
| 1 | Deployed to 500+ employees | Case Study | [ ] True / [ ] False |
| 2 | Zero PII leaks in 6 months production | Case Study | [ ] True / [ ] False |
| 3 | 80% adoption rate achieved | Metrics | [ ] True / [ ] False |

Human response: "FALSE - Client only saw a demo. None of this happened."
```

**Lesson:** Without HITL, Clarity Gate can make false claims WORSE by adding authoritative-looking source markers.

---

## Related

- **[Source of Truth Creator](https://github.com/frmoretto/source-of-truth-creator)** -- Create documents (use before verification)
- **[Stream Coding](https://github.com/frmoretto/stream-coding)** -- Full documentation-first methodology
- **Author:** Francesco Marinoni Moretto

---

## Changelog

### v1.4 (2025-12-23)
- Added: Annotation offer after verification ("Would you like me to produce an annotated version?")
- Added: CGD (Clarity-Gated Document) output mode
- Bridges gap between checker and annotator functionality

### v1.3 (2025-12-21)
- Restructured: Points grouped into "Epistemic Checks (1-4)" and "Data Quality Checks (5-7)"
- Added: Detection vs enforcement distinction in intro
- Added: Tier 1A (Internal Consistency) vs Tier 1B (External Verification) framework
- Updated: HITL reframed as "Intelligent Routing" with 96% efficiency example
- Added: "Trusted source of confusion" explanation for internal consistency value
- Added: Prior Art reference

### v1.2 (2025-12-21)
- Added: Step 0 in HITL - Request Source of Truth before verification
- Added: Source of Truth Template (adaptable to any domain)
- Added: Source Types table (Internal, External, None)

### v1.1 (2025-12-21)
- Added: HITL Fact Verification (mandatory)
- Added: Critical Limitation section explaining FORM vs TRUTH
- Added: HITL Failure Case Study
- Added: "Source in Doc" column to HITL table
- Updated: Output format to require HITL confirmation before PASS
- Updated: Severity levels to include "HITL REQUIRED"

### v1.0 (2025-11)
- Initial release with 6-point verification

---

**Version:** 1.4
**Scope:** Pre-ingestion verification for RAG systems and LLM document handoff
**Time:** 5-15 minutes (verification) + HITL response time (varies)
**Output:** List of issues + fixes + HITL confirmation + optional annotated document (CGD), then PASS verdict
