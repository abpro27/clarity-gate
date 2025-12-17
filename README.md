# Clarity Gate

**Pre-verification for LLM-safe documents.**

> *"Unverified claim? Mark it as uncertain. Marked uncertainty won't be hallucinated as fact."*

---

## What Is This?

Clarity Gate is a verification skill that checks if a document will cause another LLM to hallucinate when reading it.

**The problem:** When you hand a document to an LLM (for summarization, analysis, or as context), the LLM can't tell the difference between verified facts and your hypotheses. It will confidently repeat your guesses as if they were proven truths.

**The solution:** Run Clarity Gate before sharing documents with AI systems. It flags claims that need uncertainty markers.

---

## Critical Limitation

> **Clarity Gate verifies FORM, not TRUTH.**

This skill checks whether claims are properly marked as uncertain—it cannot verify if claims are actually true.

**Risk:** An LLM can hallucinate facts INTO a document, then "pass" Clarity Gate by adding source markers to false claims.

**Solution:** Point 7 (HITL Fact Verification) is now **MANDATORY** before declaring PASS.

---

## The Core Insight

LLMs don't hallucinate randomly. They hallucinate **from your documents** when those documents contain unmarked assumptions.

| What You Wrote | What LLM Says | Problem |
|----------------|---------------|---------|
| "This reduces latency by 40%" | "The system achieves 40% latency reduction" | You projected, LLM stated as fact |
| "Method B is superior" | "Method B has been proven superior" | Your opinion became "proven" |
| "Users prefer the new design" | "User research shows preference for..." | 3 interviews became "research" |

**Fix:** Mark uncertainty at the source. The LLM can't hallucinate what's already hedged.

---

## Quick Start

### Option 1: claude.ai / Claude Desktop

1. Download `clarity-gate.zip` from this repo
2. Go to Settings → Capabilities → Skill → Add
3. Upload the zip file
4. Ask Claude: *"Run clarity gate on this document"*

### Option 2: Claude Code

1. Copy `SKILL.md` to your project's skills folder
2. Claude Code will automatically detect and use it
3. Ask Claude: *"Run clarity gate on this document"*

### Option 3: Manual Checklist

For other LLMs or manual verification, run through your document looking for:

| Pattern | Action |
|---------|--------|
| Specific percentages (89%, 73%) | Add source or mark "(estimated)" |
| Comparison tables | Add "PROJECTED" header |
| "Achieves", "delivers", "provides" | Change to "designed to", "intended to" |
| ✅ checkmarks | Verify these are actually confirmed |
| "100%" anything | Almost always needs qualification |
| **Case studies / customer names** | **⚠️ HITL REQUIRED** |
| **Production deployments** | **⚠️ HITL REQUIRED** |
| **Measured outcomes** | **⚠️ HITL REQUIRED** |

---

## Example

**Before:**
> "Our new architecture reduces latency by 40% and improves throughput by 3x."

**After:**
> "Our new architecture is PROJECTED to reduce latency by ~40% and improve throughput by ~3x (UNTESTED — based on design calculations, not production measurements)."

**Why it matters:** An LLM reading the "before" version will confidently cite "40% latency reduction" as fact. The "after" version preserves your uncertainty.

---

## When to Use

- Before sharing specs with AI coding assistants
- Before feeding documents to RAG systems
- Before LLM summarization
- When handing off documentation between sessions
- After writing anything with projections or estimates

---

## Part of Stream Coding

Clarity Gate is extracted from [Stream Coding v3.3](https://github.com/frmoretto/stream-coding) — a documentation-first methodology for AI-accelerated development.

In Stream Coding, Clarity Gate is the mandatory checkpoint between documentation (Phase 2) and execution (Phase 3). No code generation until specs pass the gate.

This standalone version lets you use the verification without the full methodology.

---

## License

CC BY 4.0 — Use freely, attribution appreciated.

---

## Author

**Francesco Marinoni Moretto**
- GitHub: [@frmoretto](https://github.com/frmoretto)
- LinkedIn: [francesco-moretto](https://www.linkedin.com/in/francesco-moretto/)

---

# Complete Verification Reference

*The sections below contain the full operational details for running Clarity Gate.*

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

**Why this exists:** Points 1-6 verify that claims are MARKED correctly. They cannot verify if claims are TRUE.

**Check:** Extract ALL major factual claims and present them to the human for verification.

**Process:**

1. **Extract claims** - List every significant factual assertion
2. **Present to human** in table format with "Source in Doc" column
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

## Changelog

### v1.2 (December 2025)
- **ADDED:** Step 0 in Point 7 - Request Source of Truth before HITL
- **ADDED:** Source of Truth Template (adaptable to any domain)
- **ADDED:** Source Types table (Internal, External, None)

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
**Last Updated:** December 2025
