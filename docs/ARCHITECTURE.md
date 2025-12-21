# Clarity Gate Architecture

**Version:** 1.0  
**Last Updated:** 2025-12-21

---

## Overview

Clarity Gate is a pre-ingestion verification system for epistemic quality. This document details the verification architecture, including the 7-point checklist and tiered verification hierarchy.

---

## The 7 Verification Points

### Epistemic Checks (Core Focus)

These four checks address the primary mission: ensuring claims are properly qualified.

#### 1. Hypothesis vs. Fact Labeling

**Question:** Is this claim marked as validated or hypothetical?

| Fails | Passes |
|-------|--------|
| "Our architecture outperforms competitors" | "Our architecture outperforms competitors [benchmark data in Table 3]" |
| "The model achieves 40% improvement" | "The model achieves 40% improvement [measured on dataset X]" |
| "Users prefer this approach" | "Users prefer this approach [n=50 survey, p<0.05]" |

**Why it matters:** Ungrounded assertions look like facts to downstream systems.

---

#### 2. Uncertainty Marker Enforcement

**Question:** Do forward-looking statements have appropriate qualifiers?

| Fails | Passes |
|-------|--------|
| "Revenue will be $50M by Q4" | "Revenue is **projected** to be $50M by Q4" |
| "The feature will reduce churn" | "The feature is **expected** to reduce churn" |
| "We will achieve product-market fit" | "We **aim** to achieve product-market fit" |

**Keywords to enforce:** projected, expected, estimated, anticipated, planned, aimed, targeted

**Why it matters:** Future states stated as present facts become "verified" hallucinations.

---

#### 3. Assumption Visibility

**Question:** Are implicit assumptions made explicit?

| Fails | Passes |
|-------|--------|
| "The system scales linearly" | "The system scales linearly [assuming <1000 concurrent users]" |
| "Response time is 50ms" | "Response time is 50ms [under standard load conditions]" |
| "Cost per user is $0.02" | "Cost per user is $0.02 [at current AWS pricing, us-east-1]" |

**Why it matters:** Hidden assumptions break when conditions change.

---

#### 4. Authoritative-Looking Unvalidated Data

**Question:** Do tables/charts with specific numbers have validation sources?

| Red Flags | Resolution |
|-----------|------------|
| Table with percentages, no source | Add [source] or mark [PROJECTED] |
| Chart with trend lines, no methodology | Add methodology note |
| Comparison matrix with checkmarks | Clarify if measured or claimed |

**Why it matters:** Formatted data triggers authority heuristics. Tables "look" more credible than prose.

---

### Data Quality Checks (Complementary)

These three checks support epistemic quality by catching data issues.

#### 5. Data Consistency

**Question:** Are there conflicting numbers, dates, or facts within the document?

| Check Type | Example Discrepancy |
|------------|---------------------|
| Figure vs. Text | Figure shows beta=0.33, text claims beta=0.73 |
| Abstract vs. Body | Abstract claims "40% improvement," body shows 28% |
| Table vs. Prose | Table lists 5 features, text references 7 |
| Repeated values | Revenue stated as $47M in one section, $49M in another |

**Why it matters:** Internal contradictions indicate unreliable content.

---

#### 6. Implicit Causation

**Question:** Does the claim imply causation without evidence?

| Fails | Passes |
|-------|--------|
| "Feature X increased retention" | "Feature X **correlated with** increased retention" |
| "The change reduced errors" | "Errors decreased **after** the change [causal link not established]" |
| "Training improved performance" | "Performance improved **following** training [controlled study pending]" |

**Why it matters:** Correlation stated as causation misleads decision-making.

---

#### 7. Future State as Present

**Question:** Are planned outcomes described as if already achieved?

| Fails | Passes |
|-------|--------|
| "The system handles 10K requests/second" | "The system **is designed to** handle 10K requests/second" |
| "We have enterprise customers" | "We **are targeting** enterprise customers" |
| "The API supports GraphQL" | "The API **will support** GraphQL [Q2 roadmap]" |

**Why it matters:** Aspirations presented as reality create false expectations.

---

## Verification Hierarchy

```
Claim Extracted --> Does Source of Truth Exist?
                           |
           +---------------+---------------+
           YES                             NO
           |                               |
     Tier 1: Automated              Tier 2: HITL
     Verification                   (Last Resort)
           |                               |
     +-----+-----+                   Human reviews:
     |           |                   - Add markers
   Tier 1A    Tier 1B               - Provide source
   Internal   External              - Reject claim
           |           |                   |
     PASS/BLOCK   PASS/BLOCK        APPROVE/REJECT
```

---

## Tier 1A: Internal Consistency (Ready Now)

Checks for contradictions *within* a document. No external systems required.

### Capabilities

| Check | Description | Status |
|-------|-------------|--------|
| Figure vs. Text | Cross-reference numerical claims | Ready |
| Abstract vs. Body | Verify summary matches content | Ready |
| Table vs. Prose | Ensure counts/lists are consistent | Ready |
| Duplicate values | Flag conflicting repeated claims | Ready |

### Implementation

The Claude skill implementation handles Tier 1A checks through:
1. Extracting claims from document
2. Cross-referencing numerical values
3. Flagging discrepancies with specific locations

### Example Output

```yaml
check: internal_consistency
status: DISCREPANCY_FOUND

findings:
  - type: figure_vs_text
    figure_location: Figure 3, panel B
    figure_value: "beta = 1/3 = 0.33"
    text_location: Section 4.2, paragraph 3
    text_value: "beta = 11/15 = 0.73"
    delta: 0.40
    severity: HIGH

action: BLOCK - Resolve discrepancy before ingestion
```

---

## Tier 1B: External Verification (Extension Interface)

For claims verifiable against structured sources. **Users implement connectors.**

### Interface Design

```python
class VerificationConnector:
    """Base class for external verification."""
    
    def can_verify(self, claim: Claim) -> bool:
        """Returns True if this connector can verify the claim type."""
        pass
    
    def verify(self, claim: Claim) -> VerificationResult:
        """Verifies claim against external source."""
        pass
```

### Example Connectors (User-Implemented)

| Claim Type | Source | Connector |
|------------|--------|-----------|
| "Q3 revenue was $47M" | Financial system | `FinancialDataConnector` |
| "Feature deployed Oct 15" | Git commits | `GitHistoryConnector` |
| "Customer count is 1,247" | CRM | `CRMConnector` |
| "API latency is 50ms" | Monitoring | `MetricsConnector` |

### Honest Limitation

External verification requires bespoke integration for each data source. This is **not out-of-the-box functionality**. Clarity Gate provides the interface; users provide implementations.

---

## Tier 2: HITL Fallback (Intelligent Routing)

When automated verification cannot resolve a claim, it routes to human review.

### The Value Proposition

The value isn't having humans review data -- every team does that.

The value is **intelligent routing**: the system detects *which specific claims* need human review, sparing humans from reviewing safe ones.

### Efficiency Example

*A 50-claim document might have 48 pass automated checks. The system routes only 2 for human review, reducing manual effort by ~96%. (Illustrative example, not measured.)*

### Human Review Options

When a claim is routed to HITL, the human must:

1. **Provide Source of Truth** -- Point to authoritative source that was missed
2. **Add Epistemic Markers** -- Mark as [PROJECTION], [HYPOTHESIS], [UNVERIFIED]
3. **Reject Claim** -- Remove or rewrite the claim entirely

### HITL Protocol

```yaml
claim: "Our system achieves 99.9% uptime"
automated_result: CANNOT_VERIFY
reason: No source of truth for uptime metrics

human_action_required:
  options:
    - provide_source: "Link to monitoring dashboard or SLA report"
    - add_marker: "Mark as [TARGET] or [PROJECTED]"
    - reject: "Remove claim or rewrite with evidence"
  
  deadline: Before document enters knowledge base
```

---

## Output Format

### Summary Block

```yaml
verification_result:
  status: PASS | FAIL | NEEDS_REVIEW
  document: "[filename]"
  timestamp: "[ISO-8601]"
  
  summary:
    total_claims: [n]
    passed: [n]
    failed: [n]
    needs_review: [n]
```

### Detailed Findings

```yaml
findings:
  - id: 1
    claim: "[exact text]"
    location: "[section/paragraph]"
    check: "[which of 7 checks]"
    result: PASS | FAIL | NEEDS_REVIEW
    reason: "[explanation]"
    suggested_fix: "[how to resolve]"
```

### Final Determination

```yaml
determination:
  action: APPROVE | BLOCK | ROUTE_TO_HITL
  blocking_issues: [list if any]
  hitl_required: [list if any]
```

---

## Critical Limitation

> **Clarity Gate verifies FORM, not TRUTH.**

This system checks whether claims are properly marked as uncertain -- it cannot verify if claims are actually true.

### The Risk

An LLM can hallucinate facts INTO a document, then "pass" Clarity Gate by adding source markers to false claims.

Example:
```
FAIL: "Revenue will be $50M"
PASS: "Revenue is projected to be $50M [source: Q3 planning doc]"
```

The second passes Clarity Gate even if the "Q3 planning doc" doesn't exist or says something different.

### The Mitigation

HITL Fact Verification is **MANDATORY** before declaring PASS. The human must:
1. Spot-check that cited sources actually exist
2. Verify cited sources actually support the claims
3. Flag any suspicious attribution patterns

---

## Integration Points

### As Claude Skill

Primary implementation. See [SKILL.md](../SKILL.md).

### As LlamaIndex Component (Planned)

```python
from clarity_gate import ClarityGateChecker

checker = ClarityGateChecker()
result = checker.verify(document)

if result.status == "PASS":
    index.insert(document)
else:
    route_to_review(document, result.findings)
```

### As LangChain Tool (Planned)

```python
from langchain.tools import ClarityGateTool

tools = [ClarityGateTool()]
agent = create_agent(tools=tools)
```

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | 2025-12-21 | Initial architecture document |

---

## Related Documents

- [SKILL.md](../SKILL.md) -- Claude skill implementation
- [PRIOR_ART.md](PRIOR_ART.md) -- Landscape of existing systems
- [ROADMAP.md](ROADMAP.md) -- Development phases
