---
name: clarity-gate
description: Document verification to prevent LLM hallucination and equivocation. Use when reviewing documents that will be read by other LLMs, checking if claims could be misinterpreted as facts, or validating that hypotheses are clearly marked. Triggers on "clarity gate", "check for hallucination risks", "can an LLM read this safely", "review for equivocation", "verify document clarity".
---

# Clarity Gate v1.2

**Purpose:** Verify a document won't cause another LLM to hallucinate or equivocate when reading it.

**Core Question:** "If another LLM reads this document, will it mistake assumptions for facts?"

**Core Principle:** *"Unverified claim? Mark it as uncertain. Marked uncertainty won't be hallucinated as fact."*

**Origin:** Extracted from Stream Coding v3.3 methodology by Francesco Marinoni Moretto. Stream Coding uses Clarity Gate as a mandatory checkpoint before code generation. This standalone version enables the verification pass without the full methodology.

---

## Critical Limitation

> **Clarity Gate verifies FORM, not TRUTH.**
>
> This skill checks whether claims are properly marked as uncertain—it cannot verify if claims are actually true. 
>
> **Risk:** An LLM can hallucinate facts INTO a document (from misremembered conversations, incorrect assumptions, or pattern-matching errors), then "pass" Clarity Gate by adding source markers to false claims.
>
> **Solution:** The HITL (Human-In-The-Loop) verification step (Point 7) is now **MANDATORY** before declaring PASS.

---

## When to Use

- Before sharing documents with other AI systems
- After writing specifications, state docs, or methodology descriptions
- When a document contains projections, estimates, or hypotheses
- Before publishing claims that haven't been validated
- When handing off documentation between LLM sessions

---

## The 7-Point Verification

### 1. DATA CONSISTENCY
Scan for conflicting numbers, dates, or facts within the document.

**Check:** Does the same metric appear with different values?
**Red flag:** "500 users" in one section, "750 users" in another

**Fix:** Reconcile conflicts or explicitly note the discrepancy with explanation.

---

### 2. HYPOTHESIS vs FACT LABELING
Every claim must be clearly marked as either validated or hypothetical.

**Check:** Are projections, estimates, and assumptions explicitly labeled?
**Red flag:** "This achieves 40% improvement" (stated as fact, but untested)

**Fix:** Add markers: "PROJECTED:", "HYPOTHESIS:", "UNTESTED:", "(estimated)", "~", "?"

---

### 3. AUTHORITATIVE-LOOKING UNVALIDATED DATA
Tables with specific percentages and ✅/❌ symbols look like measured data.

**Check:** Could a reader mistake this table/chart for empirical results?
**Red flag:** 
| Approach | Accuracy | Success Rate |
|----------|----------|--------------|
| Method A | 89% | 66% |
| Method B | 95% | 100% |

**Fix:** Add "(guess)", "(est.)", "?" to numbers. Change ✅ to ❓ for unvalidated items. Add explicit warning above table.

---

### 4. IMPLICIT CAUSATION
Claims that imply causation without evidence.

**Check:** Does the document claim X causes Y without validation?
**Red flag:** "Shorter prompts improve response quality" (plausible but unproven)

**Fix:** Reframe as hypothesis: "Shorter prompts MAY improve response quality (hypothesis, not validated)"

---

### 5. FUTURE STATE AS PRESENT
Describing planned/hoped outcomes as if already achieved.

**Check:** Are future goals written in present tense?
**Red flag:** "The system processes 10,000 requests per second" (when it hasn't been built)

**Fix:** Use future/conditional: "The system is DESIGNED TO process..." or "TARGET: 10,000 rps"

---

### 6. MISSING UNCERTAINTY MARKERS
Sections that should have caveats but don't.

**Check:** Marketing hooks, executive summaries, "key takeaways" - do they acknowledge limitations?
**Red flag:** 
> "Our approach delivers 50% cost reduction and 99.9% accuracy."

**Fix:** Add status indicators:
> "Our approach is DESIGNED TO deliver... (metrics PROJECTED, not measured)"

---

### 7. HITL FACT VERIFICATION ⚠️ MANDATORY

**Step 0: Request Source of Truth**

Before extracting claims, ask:

> "Do you have a Source of Truth document for this project?
> (e.g., project state record, verified metrics, status tracker)
>
> If yes, please share it — I'll use it to verify claims.
> If no, I'll present claims for manual verification."

---

**Why this exists:** Points 1-6 verify that claims are MARKED correctly. They cannot verify if claims are TRUE. An LLM may confidently write false information derived from:
- Misremembered conversations
- Incorrect pattern-matching from context
- Confusing proposals/plans with completed work
- Assuming demo = deployment, or planned = achieved

**Check:** Extract ALL major factual claims and present them to the human for verification.

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
   | 1 | [specific claim] | [section/line] | ⬜ True / ⬜ False |
   | 2 | [specific claim] | [section/line] | ⬜ True / ⬜ False |
   | 3 | [specific claim] | [section/line] | ⬜ True / ⬜ False |
   
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

**Fix:** If human identifies false claims, either:
- Remove the claim entirely
- Reframe as "proposed", "planned", "hypothesized"
- Add explicit "NOT VALIDATED" warning

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
| [claim] | None | ⚠️ Unverified |

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
|------|------------|--------|
| **Internal** | Verified state of this project/context | Metrics, status records, team data |
| **External** | Published research, third-party data | Academic papers, industry reports, official statistics |
| **None** | Claim without supporting source | ⚠️ Requires HITL verification |

---

## Quick Scan Checklist

Run through document looking for these patterns:

| Pattern | Action |
|---------|--------|
| Specific percentages (89%, 73%, etc.) | Add source or mark as estimate |
| Comparison tables | Add "PROJECTED" header + uncertainty markers |
| "Achieves", "delivers", "provides" | Check if validated; if not, use "designed to", "intended to" |
| ✅ / ✓ checkmarks | Verify these are actually confirmed |
| "100%" anything | Almost always needs qualification |
| Time/cost savings claims | Mark as projected unless measured |
| "The model recognizes/understands/knows" | Mark as hypothesis about LLM behavior |
| "Always", "never", "guarantees" | Check if absolute claim is warranted |
| **Case studies / customer names** | **⚠️ HITL REQUIRED - verify with human** |
| **Production deployments** | **⚠️ HITL REQUIRED - verify with human** |
| **Measured outcomes** | **⚠️ HITL REQUIRED - verify with human** |

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

### Passed (Points 1-6)
- [what was already clear]

---

## ⚠️ HITL Verification Required (Point 7)

Before declaring PASS, please confirm these claims are TRUE:

| # | Claim | Source in Doc | Human Confirms |
|---|-------|---------------|----------------|
| 1 | [claim] | [location] | ⬜ True / ⬜ False |
| 2 | [claim] | [location] | ⬜ True / ⬜ False |

**Verdict:** PENDING HITL CONFIRMATION
```

**Only after human confirms can you update to:**

```
**Verdict:** PASS (HITL confirmed [date])
```

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

- ❌ Classify document types (use Stream Coding for that)
- ❌ Restructure documents 
- ❌ Add deep links or references
- ❌ Evaluate writing quality
- ❌ **Check factual accuracy autonomously** (requires HITL for factual claims)

**This skill verifies:** 
1. (Points 1-6) Can another LLM read this without mistaking assumptions for facts?
2. (Point 7) Has a human confirmed the factual claims are actually true?

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
3. Ran Clarity Gate points 1-6
4. Added "(client-reported)" marker to claims
5. Declared PASS

**The problem:**
- The client only saw a demo—there was NO production deployment
- The LLM misinterpreted past conversations
- Adding "(client-reported)" made a FALSE claim look MORE credible
- Clarity Gate verified the FORM but not the TRUTH

**What HITL would have caught:**

```
## HITL Verification Required

| # | Claim | Source in Doc | Human Confirms |
|---|-------|---------------|----------------|
| 1 | Deployed to 500+ employees | Case Study | ⬜ True / ⬜ False |
| 2 | Zero PII leaks in 6 months production | Case Study | ⬜ True / ⬜ False |
| 3 | 80% adoption rate achieved | Metrics | ⬜ True / ⬜ False |

Human response: "FALSE - Client only saw a demo. None of this happened."
```

**Lesson:** Without HITL, Clarity Gate can make false claims WORSE by adding authoritative-looking source markers.

---

## Related

- **Stream Coding v3.3** — Full documentation-first methodology that includes Clarity Gate as Phase 2→3 checkpoint
- **Author:** Francesco Marinoni Moretto

---

## Changelog

### v1.2 (December 2025)
- **ADDED:** Step 0 in Point 7 - Request Source of Truth before HITL
- **ADDED:** Source of Truth Template (adaptable to any domain)
- **ADDED:** Source Types table (Internal, External, None)
- **ADDED:** Key principles for Source of Truth documents

### v1.1 (December 2025)
- **ADDED:** Point 7 - HITL Fact Verification (MANDATORY)
- **ADDED:** Critical Limitation section explaining FORM vs TRUTH
- **ADDED:** HITL Failure Case Study
- **ADDED:** "Source in Doc" column to HITL table for easier verification
- **UPDATED:** Output format to require HITL confirmation before PASS
- **UPDATED:** Quick Scan Checklist with HITL-required patterns
- **UPDATED:** Severity levels to include "HITL REQUIRED"

### v1.0 (November 2025)
- Initial release with 6-point verification

---

**Version:** 1.2
**Scope:** Any document that will be read by LLMs
**Time:** 5-15 minutes (verification) + HITL response time (varies)
**Output:** List of issues + fixes + HITL confirmation, then PASS verdict
