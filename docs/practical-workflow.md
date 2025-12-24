# Practical Workflow (Hypothesized)

**Status:** Hypothesized — test on your documents before deploying

If the annotation effect holds on real documents:

```
RAW DOCUMENT
     │
     ▼
┌─────────────────┐
│  TOP-TIER MODEL │  ← Claude, Gemini Pro (one-time)
│  "Annotator"    │
└────────┬────────┘
         │
         ▼
ANNOTATED DOCUMENT
     │
     ▼
┌─────────────────┐
│  MID-TIER MODEL │  ← Gemini Flash, GPT-5 Mini (queries)
└────────┬────────┘
         │
         ▼
OUTPUT (improved abstention observed on synthetic benchmark)
```

## Caveat

This workflow is hypothesized. Before deploying:
1. Test on your actual documents
2. Compare against system-prompt baselines
3. Measure whether annotations improve abstention in your domain

## Implementation

The Claude Skill ([SKILL.md](https://github.com/frmoretto/clarity-gate/blob/main/SKILL.md)) implements the annotator role.

**Easiest way to test:**
1. Download the skill
2. Add to Claude Desktop (Settings → Features → Skills)
3. Upload any document
4. Ask "Run clarity gate on this document"

## Relationship to Self-RAG

Self-RAG operates at generation-time; annotations operate at ingestion-time. Orthogonal approaches that could potentially be combined.
