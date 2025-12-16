---
name: clarity-gate
description: Document verification to prevent LLM hallucination and equivocation. Use when reviewing documents that will be read by other LLMs, checking if claims could be misinterpreted as facts, or validating that hypotheses are clearly marked. Triggers on "clarity gate", "check for hallucination risks", "can an LLM read this safely", "review for equivocation", "verify document clarity".
---

# Clarity Gate v1.0

**Purpose:** Verify a document won't cause another LLM to hallucinate or equivocate when reading it.

**Core Question:** "If another LLM reads this document, will it mistake assumptions for facts?"

**Core Principle:** *"Unverified claim? Mark it as uncertain. Marked uncertainty won't be hallucinated as fact."*

**Origin:** Extracted from Stream Coding v3.3 methodology by Francesco Marinoni Moretto. Stream Coding uses Clarity Gate as a mandatory checkpoint before code generation. This standalone version enables the verification pass without the full methodology.

---

## When to Use

- Before sharing documents with other AI systems
- After writing specifications, state docs, or methodology descriptions
- When a document contains projections, estimates, or hypotheses
- Before publishing claims that haven't been validated
- When handing off documentation between LLM sessions

---

## The 6-Point Verification

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

### Passed
- [what was already clear]

**Verdict:** PASS / NEEDS FIXES / FAIL
```

---

## Severity Levels

| Level | Definition | Action |
|-------|------------|--------|
| **CRITICAL** | LLM will likely treat hypothesis as fact | Must fix before use |
| **WARNING** | LLM might misinterpret | Should fix |
| **PASS** | Clearly marked, no ambiguity | No action needed |

---

## What This Skill Does NOT Do

- ❌ Classify document types (use Stream Coding for that)
- ❌ Restructure documents 
- ❌ Add deep links or references
- ❌ Evaluate writing quality
- ❌ Check factual accuracy (only checks if uncertainty is marked)

**This skill only verifies:** Can another LLM read this without mistaking assumptions for facts?

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

---

## Related

- **Stream Coding v3.3** — Full documentation-first methodology that includes Clarity Gate as Phase 2→3 checkpoint
- **Author:** Francesco Marinoni Moretto

---

**Version:** 1.0
**Scope:** Any document that will be read by LLMs
**Time:** 5-15 minutes depending on document length
**Output:** List of issues + fixes, or PASS verdict
