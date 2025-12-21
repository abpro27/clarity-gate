# Clarity Gate Research — Source of Truth

> **Created with:** [Source of Truth Creator](https://github.com/frmoretto/source-of-truth-creator)
>
> This document follows the six failure mode mitigations prescribed by the Source of Truth Creator skill.

**Last Updated:** 2025-12-21  
**Owner:** Francesco Marinoni Moretto  
**Status:** VERIFIED (see Verification Status below)  
**Version:** 2.0

---

## Verification Status

| Category | Status | Confidence | Staleness Risk |
|----------|--------|------------|----------------|
| External claims (academic papers) | ✅ Verified against sources | High | STABLE |
| External claims (enterprise products) | ✅ Verified against websites | High | CHECK BEFORE CITING |
| External claims (competitor features) | ✅ Verified [2025-12-21] | High | VOLATILE |
| Internal claims (project state) | ✅ Verified by owner | High | Low |
| Internal measurements (timing) | ⚠️ Informal measurement | Medium | Low |
| Gap claims ("no X exists") | ⚠️ Systematic search | Medium | Medium |
| Projections and estimates | ⚠️ Marked as such | N/A | N/A |
| Self-assessments (scores) | ⚠️ Author judgment | N/A | N/A |

**Last verified:** 2025-12-21 by Francesco Marinoni Moretto  
**Exceptions:** Internal timing measurements are single-run informal tests, not rigorous benchmarks  
**Re-verify before use:** Adlib features, competitor pricing, all URLs

---

## Purpose

Consolidated research, prior art analysis, and strategic positioning for Clarity Gate. This document serves as the authoritative reference for all claims made in the LessWrong post, README, and SKILL.md.

---

## Verified Data

### External Sources — Stable (Academic, Standards, Historical)

| Claim | Value | Source | Reference | Verified | Staleness |
|-------|-------|--------|-----------|----------|-----------|
| UnScientify accuracy | ~0.8 on research articles (approximate) | Ningrum et al., 2023 | arXiv:2307.14236 | 2025-12-21 | STABLE |
| HedgeHog F1 score | ~0.84-0.90 per class | HuggingFace model | jeniakim/hedgehog | 2025-12-21 | STABLE |
| Self-RAG methodology | Reflection tokens (ISREL, ISSUP, ISUSE) | Asai et al., 2023 | arXiv:2310.11511 | 2025-12-21 | STABLE |
| FEVER dataset | Claim → evidence → verification pipeline | Thorne et al., 2018 | fever.ai | 2025-12-21 | STABLE |
| FDA 21 CFR Part 11 | Electronic records compliance | FDA regulations | Established 1997 | 2025-12-21 | STABLE |
| ISO/IEC 5259-1:2024, 5259-2:2024 | AI data quality for analytics and ML | ISO standard | iso.org | 2025-12-21 | STABLE |
| Pharma QMS regulatory standard | 20+ years | FDA 21 CFR Part 11 | Established 1997 | 2025-12-21 | STABLE |

### External Sources — Volatile (Products, Features, Pricing)

| Claim | Value | Source | Reference | Verified | Staleness |
|-------|-------|--------|-----------|----------|-----------|
| Adlib Transform release | November 18, 2025 | Press release | adlibsoftware.com/news/... | 2025-12-21 | CHECK BEFORE CITING |
| Adlib exception rate reduction | 40-60% claimed | Transform 2025.2 marketing | Press release | 2025-12-21 | VOLATILE — marketing claim |
| Adlib cycle time acceleration | 30-50% claimed | Transform 2025.2 marketing | Press release | 2025-12-21 | VOLATILE — marketing claim |
| Adlib customers | Pfizer, Swiss Re, IAG, JP Morgan | Website | adlibsoftware.com/about-us | 2025-12-21 | CHECK BEFORE CITING |
| Adlib certifications | SOC 2 Type II, HIPAA | October 2025 | Press materials | 2025-12-21 | CHECK BEFORE CITING |
| SimplerQMS exists | FDA 21 CFR Part 11 compliance | Website | simplerqms.com | 2025-12-21 | VOLATILE — URL may change |
| Dot Compliance exists | AI-powered eQMS | Website | dotcompliance.com | 2025-12-21 | VOLATILE — URL may change |

### Internal Sources

| Claim | Value | Methodology | Confidence | Verified |
|-------|-------|-------------|------------|----------|
| SKILL.md current version | v1.3 | File inspection | High | 2025-12-21 |
| Previous SKILL.md version | v1.2 | Changelog review | High | 2025-12-21 |
| Number of verification points | 7 (4 epistemic + 3 data quality) | Document review | High | 2025-12-21 |
| Tier 1A/1B distinction created | 2025-12-21 | This research process | High | 2025-12-21 |
| Intelligent routing concept created | 2025-12-21 | This research process | High | 2025-12-21 |

### Internal Measurements — Informal

**⚠️ These are single-run informal measurements, not rigorous benchmarks.**

| Claim | Value | Methodology | Confidence | Verified |
|-------|-------|-------------|------------|----------|
| Biology paper discrepancy detection | ~12 seconds | Single pipeline run, informal timing | Medium | 2025-12-21 |
| Figure vs text discrepancy | Δ = 0.40 (β=0.33 vs β=0.73) | Single document analysis | High | 2025-12-21 |

### Estimates and Projections (NOT VERIFIED DATA)

| Claim | Value | Basis | Status |
|-------|-------|-------|--------|
| Manual review time | ~45 minutes | Author estimate for cross-referencing equations/figures | ⚠️ ESTIMATED |
| HITL efficiency improvement | ~96% | Illustrative example (48/50 claims pass) | ⚠️ ILLUSTRATIVE EXAMPLE |

---

## Status Tracker

### Prior Art Systems

| System | Category | Pre-ingestion? | Open Source? | Epistemic Focus? | Status |
|--------|----------|----------------|--------------|------------------|--------|
| Adlib Software | Enterprise | ✅ Yes | ❌ No | ❌ No (accuracy/compliance) | Verified |
| SimplerQMS | Pharma QMS | ✅ Yes | ❌ No | ❌ No (compliance) | Verified |
| Dot Compliance | Pharma QMS | ✅ Yes | ❌ No | ❌ No (compliance) | Verified |
| Picomto | Pharma QMS | ✅ Yes | ❌ No | ❌ No (compliance) | Verified |
| Ideagen | Pharma QMS | ✅ Yes | ❌ No | ❌ No (compliance) | Verified |
| UnScientify | Detection | ❌ No | ✅ Yes | ⚠️ Partial (detection only) | Verified |
| HedgeHog | Detection | ❌ No | ✅ Yes | ⚠️ Partial (detection only) | Verified |
| FactBank | Corpus | ❌ No | ✅ Yes | ⚠️ Partial (training data) | Verified |
| BioScope | Corpus | ❌ No | ✅ Yes | ⚠️ Partial (training data) | Verified |
| FEVER | Fact-checking | ❌ No | ✅ Yes | ❌ No (verification pipeline) | Verified |
| ClaimBuster | Fact-checking | ❌ No | ✅ Yes | ❌ No (claim detection) | Verified |
| Self-RAG | Runtime | ❌ No | ✅ Yes | ⚠️ Partial (post-retrieval) | Verified |
| RAGAS | Evaluation | ❌ No | ✅ Yes | ⚠️ Partial (post-retrieval) | Verified |
| TruLens | Evaluation | ❌ No | ✅ Yes | ⚠️ Partial (post-retrieval) | Verified |
| Protecto.ai | Privacy | ✅ Yes | ❌ No | ❌ No (PII/PHI) | Verified |
| Guardrails AI | Validation | ⚠️ Partial | ✅ Yes | ❌ No (schema/format) | Verified |
| NeMo Guardrails | Validation | ⚠️ Partial | ✅ Yes | ❌ No (dialog safety) | Verified |

### Clarity Gate Components

| Component | Status | Notes |
|-----------|--------|-------|
| 7-point verification system | Completed | 4 epistemic + 3 data quality |
| Tier 1A: Internal consistency | Completed | Ready for use |
| Tier 1B: External verification interface | Planned | Design phase, not implemented |
| Tier 2: HITL intelligent routing | Completed | Protocol defined |
| Source of Truth template | Completed | Adaptable format |
| HITL failure case study | Completed | Documented in SKILL.md |
| LessWrong post | Completed | v6.3 final |
| Elevated README | Completed | Strategic positioning |
| SKILL.md v1.3 | Completed | All updates integrated |
| GitHub repository update | Completed | Files replaced, pending push |
| Phase 2 external hooks | Planned | Architecture defined, not implemented |
| Phase 3 confidence scoring | Planned | Concept only |

---

## Gap Analysis (Author Assessment)

**Note:** Gap claims reflect author's systematic search as of 2025-12-21. Absence of evidence is not equal to evidence of absence.

| Domain | Existing Solutions | Gap Identified | Confidence |
|--------|-------------------|----------------|------------|
| Pre-ingestion + Accuracy | Adlib, Pharma QMS | None — solved at enterprise scale | High |
| Pre-ingestion + Epistemic | None found | Open-source epistemic enforcement | Medium (systematic search 2025-12) |
| Detection + Epistemic | UnScientify, HedgeHog | None — detection exists | High |
| Post-retrieval + Epistemic | RAGAS, TruLens, Self-RAG | None — evaluation exists | High |

### The Gap Matrix (Author Assessment)

| Stage | Privacy/Security | Accuracy/Compliance | Epistemic Quality |
|-------|------------------|---------------------|-------------------|
| **Pre-ingestion** | ✅ Protecto.ai, OWASP | ✅ Adlib, Pharma QMS | ❌ **Gap** (to author's knowledge) |
| **Detection** | — | — | ✅ UnScientify, HedgeHog |
| **Post-retrieval** | — | — | ✅ RAGAS, TruLens |
| **Runtime** | — | — | ✅ Self-RAG |

**Verification method:** Systematic search across academic databases, GitHub, product websites, December 2025.

**Confidence level:** High for existence claims; Medium for "no system exists" claims (absence harder to prove).

---

## Key Distinctions (Verified)

### Accuracy vs Epistemic

| What Adlib/QMS Catch | What They Don't Catch | Source |
|----------------------|----------------------|--------|
| "Revenue was $47M" when records show $49M | "Revenue will be $50M" stated as fact | Adlib docs analysis |
| Missing required signature | "Our approach outperforms X" with no evidence | Pharma QMS analysis |
| Wrong date format | "Users prefer Y" with no methodology | Regulatory review |
| Conflicting dates across docs | Unmarked projections | Synthesis |

**Status:** Verified — based on documented feature sets of Adlib and pharma QMS systems.

### Detection vs Enforcement

| Tool Type | Question | Status |
|-----------|----------|--------|
| Detection (UnScientify, HedgeHog) | "Is uncertainty expressed?" | Verified — paper review |
| Enforcement (Clarity Gate) | "Should uncertainty be expressed but isn't?" | Verified — novel framing |

**Status:** Verified — no prior art found making this distinction explicit (to author's knowledge, Dec 2025 search).

### Open-Source vs Enterprise

| Dimension | Adlib | Clarity Gate | Source |
|-----------|-------|--------------|--------|
| License | Proprietary | CC BY 4.0 | Adlib website, our choice |
| Focus | Accuracy, compliance | Epistemic quality | Feature analysis |
| Target | Fortune 500 | Founders, researchers | Adlib marketing |
| Cost | Enterprise pricing | Free | Adlib sales model |

**Status:** Verified — direct comparison based on documented sources.

---

## Self-Assessment (Author Judgment)

**⚠️ These scores are self-assigned by the author, not externally validated.**

### Adversarial Review Scores

| Dimension | Score | Rationale |
|-----------|-------|-----------|
| Prior art handling | 9.5/10 | Acknowledged Adlib, pharma QMS, FEVER explicitly |
| Gap identification | 9.5/10 | Clear matrix showing where gap exists |
| Realism about automation | 9.5/10 | Tier 1A/1B split prevents overpromising |
| HITL framing | 9/10 | Reframed as intelligent routing, not weakness |
| Scope clarity | 9.5/10 | Honest about what's ready vs planned |
| Credibility overall | 9/10 | Evidence-based with acknowledged limitations |
| **Overall** | **8.8/10** | Weighted average |

**Note:** These scores emerged from adversarial self-review, not external audit.

---

## Unverified Claims (Require Caution)

Claims that appear in publications but require epistemic markers:

| Claim | Issue | Required Action | Status |
|-------|-------|-----------------|--------|
| "45 minutes manual review" | Author estimate, not measured | Mark as [ESTIMATED] | ✅ Marked |
| "96% HITL reduction" | Illustrative example, not measured | Mark as [ILLUSTRATIVE] | ✅ Marked |
| "Phase 2/3 architecture defined" | Conceptual, not implemented | Mark as [PLANNED] | ✅ Marked |
| "No open-source pre-ingestion epistemic gate exists" | Absence claim | Add "to author's knowledge" | ✅ Marked |

---

## Sources

### External — Stable (Academic Papers, Standards)

| Claim | Source | Reference | Last Accessed | Staleness |
|-------|--------|-----------|---------------|-----------|
| UnScientify methodology | arXiv | arXiv:2307.14236 | 2025-12-21 | STABLE |
| HedgeHog model | HuggingFace | jeniakim/hedgehog | 2025-12-21 | STABLE |
| Self-RAG methodology | arXiv | arXiv:2310.11511 | 2025-12-21 | STABLE |
| FEVER dataset | Academic | fever.ai; Thorne et al., 2018 | 2025-12-21 | STABLE |
| BioScope corpus | Academic | Vincze et al., 2008 | 2025-12-21 | STABLE |
| FDA 21 CFR Part 11 | Regulation | FDA, established 1997 | 2025-12-21 | STABLE |
| ISO/IEC 5259-1:2024, 5259-2:2024 | Standard | ISO, published 2024 | 2025-12-21 | STABLE |

### External — Volatile (Products, Websites)

| Claim | Source | Reference | Last Accessed | Staleness |
|-------|--------|-----------|---------------|-----------|
| Adlib Transform 2025.2 | Press release | adlibsoftware.com/news/... | 2025-12-21 | CHECK BEFORE CITING |
| Adlib customer list | Website | adlibsoftware.com/about-us | 2025-12-21 | CHECK BEFORE CITING |
| SimplerQMS features | Website | simplerqms.com | 2025-12-21 | VOLATILE |
| Dot Compliance features | Website | dotcompliance.com | 2025-12-21 | VOLATILE |
| ClaimBuster | Website | claimbuster.org | 2025-12-21 | VOLATILE |
| RAGAS framework | GitHub | github.com/explodinggradients/ragas | 2025-12-21 | CHECK BEFORE CITING |

### Internal (This Project)

| Claim | Source | Methodology | Verified By |
|-------|--------|-------------|-------------|
| Biology paper discrepancy | Pipeline test | Single document analysis | Francesco |
| Detection time (~12s) | Pipeline test | Informal timing, single run | Francesco |
| Gap Matrix | Research synthesis | Systematic search | Francesco |
| Tier 1A/1B distinction | Adversarial review | This research process | Francesco |

---

## Open Items

| ID | Priority | Description | Status | Owner |
|----|----------|-------------|--------|-------|
| 1 | High | Replace clarity-gate/README.md with elevated version | ✅ Completed | Francesco |
| 2 | High | Replace clarity-gate/SKILL.md with v1.3 | ✅ Completed | Francesco |
| 3 | High | Publish LessWrong post | Planned | Francesco |
| 4 | Medium | Re-zip skill for Claude upload | ✅ Completed | Francesco |
| 5 | Medium | Create docs/ folder structure | ✅ Completed | Francesco |
| 6 | Low | Add biology paper example to examples/ | ✅ Completed | Francesco |
| 7 | Low | Design Phase 2 external verification API | Planned | Francesco |
| 8 | Low | Research confidence scoring for Phase 3 | Planned | Francesco |

---

## Changelog

### v2.0 (2025-12-21)
- Restructured to follow Source of Truth Creator v1.0 skill prescriptions
- Added Verification Status box with staleness tracking
- Split External Sources into Stable vs Volatile
- Added staleness markers to all volatile claims
- Moved "45 min" and "96%" to Estimates section (not Verified Data)
- Added "to author's knowledge" to all absence claims
- Labeled self-assessment scores as author judgment
- Added "Re-verify before use" list
- Added Unverified Claims section with remediation status

### v1.0 (2025-12-21)
- Initial Source of Truth document
- Consolidated all research from December 2025

---

## Document Metadata

| Field | Value |
|-------|-------|
| Document Type | Source of Truth |
| Version | 2.0 |
| Created | 2025-12-21 |
| Last Updated | 2025-12-21 |
| Owner | Francesco Marinoni Moretto |
| Status | VERIFIED (with noted exceptions) |
| Repository | github.com/frmoretto/clarity-gate |
| Related | github.com/frmoretto/stream-coding |
| License | CC BY 4.0 |
| Created Using | Source of Truth Creator v1.0 |
| Verified Using | Clarity Gate v1.3 |
