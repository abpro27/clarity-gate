# Example: Biology Paper Discrepancy Detection

**Type:** Internal Consistency Check (Tier 1A)  
**Document:** Scientific paper on prokaryote scaling  
**Result:** DISCREPANCY FOUND

---

## The Problem

A biology paper contained conflicting values for a key parameter (β) between a figure and the text. This type of discrepancy could propagate into a RAG knowledge base and cause downstream hallucinations.

---

## What Clarity Gate Found

### Input

A scientific paper discussing scaling laws in prokaryotes, with:
- Figure 3 showing graphical data with computed β values
- Section 4.2 containing textual claims about the same β values

### Detection

```yaml
check: internal_consistency
type: figure_vs_text
status: DISCREPANCY_FOUND

findings:
  - claim_type: numerical_parameter
    parameter: β (scaling exponent)
    
    figure:
      location: Figure 3, panel B
      value: β = 1/3 ≈ 0.33
      context: "for large prokaryotes"
      
    text:
      location: Section 4.2, paragraph 3
      value: β = 11/15 = 0.73
      context: "for prokaryotes"
      
    discrepancy:
      delta: 0.40
      relative_error: 121%
      severity: HIGH
      
    assessment: >
      The text claims β = 0.73 for prokaryotes, but Figure 3 
      shows β = 0.33 for large prokaryotes. This is either:
      (a) Different populations (large vs. all prokaryotes)
      (b) An error in one of the values
      (c) Different measurement methodologies
      
      The discrepancy is significant enough to affect 
      downstream conclusions.
```

### Output

```yaml
verification_result:
  status: BLOCK
  document: "prokaryote_scaling_paper.pdf"
  timestamp: "2025-12-21T14:30:00Z"
  
  action: BLOCK - Resolve discrepancy before ingestion
  
  recommendation: >
    Before ingesting this paper into a knowledge base:
    1. Verify which β value is correct
    2. If both are correct for different populations, 
       ensure text clarifies the distinction
    3. If one is an error, obtain corrected version
```

---

## Why This Matters

### Without Clarity Gate

1. Paper ingested into RAG knowledge base
2. User asks: "What is the scaling exponent β for prokaryotes?"
3. RAG retrieves conflicting chunks:
   - Chunk A: "β = 0.33 for large prokaryotes"
   - Chunk B: "β = 0.73 for prokaryotes"
4. LLM confidently reports one or synthesizes incorrectly
5. User makes decisions based on unreliable information

### With Clarity Gate

1. Discrepancy detected at ingestion
2. Document blocked pending resolution
3. Human reviewer clarifies the discrepancy
4. Corrected/annotated version ingested
5. Knowledge base maintains consistency

---

## Performance

| Metric | Value | Notes |
|--------|-------|-------|
| Detection time | ~12 seconds | Single run, informal timing |
| Manual equivalent | ~45 minutes | Estimated time for researcher to cross-reference |
| Discrepancy found | Δ = 0.40 | 121% relative error |

**Caveat:** These are informal measurements from a single test, not rigorous benchmarks.

---

## The 7-Point Analysis

This example demonstrates several verification points:

| Check | Result | Finding |
|-------|--------|---------|
| 1. Hypothesis vs. Fact | N/A | Not the issue here |
| 2. Uncertainty Markers | N/A | Not the issue here |
| 3. Assumption Visibility | ⚠️ | Different populations may be assumed |
| 4. Authoritative-Looking Data | ⚠️ | Figure presents as authoritative |
| **5. Data Consistency** | **❌ FAIL** | **Figure vs. text mismatch** |
| 6. Implicit Causation | N/A | Not the issue here |
| 7. Future State as Present | N/A | Not the issue here |

---

## Resolution Options

### Option A: Values Are Both Correct

If β = 0.33 applies to "large prokaryotes" and β = 0.73 applies to "all prokaryotes":

**Fix:** Add clarifying text to make the distinction explicit.

```markdown
Before: "β = 11/15 = 0.73 for prokaryotes"
After:  "β = 11/15 = 0.73 for the full prokaryote population 
         (cf. β = 0.33 for large prokaryotes, Figure 3)"
```

### Option B: One Value Is an Error

If one value is incorrect:

**Fix:** Obtain corrected version of the paper, or add annotation.

```yaml
annotation:
  type: known_error
  location: Section 4.2, paragraph 3
  note: "β value may be incorrect; see Figure 3 for measured value"
  added_by: human_reviewer
  date: 2025-12-21
```

### Option C: Methodology Difference

If different measurement approaches yield different values:

**Fix:** Add methodology context to both claims.

```markdown
Figure 3: "β = 0.33 [measured via geometric scaling]"
Text: "β = 0.73 [calculated from metabolic rates]"
```

---

## Generalization

This example demonstrates the pattern for all Tier 1A checks:

1. **Extract claims** from different document locations
2. **Cross-reference** for consistency
3. **Flag discrepancies** with specific locations and values
4. **Route to human** with clear resolution options
5. **Block ingestion** until resolved

The same approach applies to:
- Abstract vs. body claims
- Table vs. prose data
- Repeated values in different sections
- Figure labels vs. captions

---

## Limitations of This Example

| Limitation | Implication |
|------------|-------------|
| Single document | May not generalize to all paper types |
| Numerical claims only | Text-based inconsistencies harder to detect |
| Informal timing | Not a benchmark |
| English only | Other languages not tested |

---

## Try It Yourself

To run this check on your own documents:

1. **Claude Desktop/Code:** Load SKILL.md, ask "Run clarity gate on [document]"
2. **Manual:** Use the 7-point checklist in ARCHITECTURE.md
3. **Programmatic:** (Phase 2) Use the verification API

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2025-12-21 | Initial example documentation |
