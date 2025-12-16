# Clarity Gate

**Pre-verification for LLM-safe documents.**

> *"Unverified claim? Mark it as uncertain. Marked uncertainty won't be hallucinated as fact."*

---

## What Is This?

Clarity Gate is a verification skill that checks if a document will cause another LLM to hallucinate when reading it.

**The problem:** When you hand a document to an LLM (for summarization, analysis, or as context), the LLM can't tell the difference between verified facts and your hypotheses. It will confidently repeat your guesses as if they were proven truths.

**The solution:** Run Clarity Gate before sharing documents with AI systems. It flags claims that need uncertainty markers.

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

### Option 1: Use as Claude Skill

1. Copy `SKILL.md` to your Claude Desktop skills folder
2. Ask Claude: *"Run clarity gate on this document"*
3. Get a report of issues + fixes

### Option 2: Manual Checklist

Run through your document looking for:

| Pattern | Action |
|---------|--------|
| Specific percentages (89%, 73%) | Add source or mark "(estimated)" |
| Comparison tables | Add "PROJECTED" header |
| "Achieves", "delivers", "provides" | Change to "designed to", "intended to" |
| ✅ checkmarks | Verify these are actually confirmed |
| "100%" anything | Almost always needs qualification |

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

## What This Skill Does NOT Do

- ❌ Classify document types (use Stream Coding for that)
- ❌ Restructure documents 
- ❌ Add deep links or references
- ❌ Evaluate writing quality
- ❌ Check factual accuracy (only checks if uncertainty is marked)

**This skill only verifies:** Can another LLM read this without mistaking assumptions for facts?

---

**Version:** 1.0  
**Last Updated:** December 2025
