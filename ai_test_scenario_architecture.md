# AI Workflow & Test Scenario Generator Agent – Technical Architecture Document

## 1. Executive Summary
The **AI Workflow & Test Scenario Generator Agent** is a Human-in-the-Loop system designed to generate **high-level test scenarios** and **structured draft test cases** from **demo transcripts** and **demo PPT slide decks**, without requiring formal requirements or system recordings. The system provides **traceable, review-ready QA artifacts**, **confidence scoring**, and **coverage gap detection**, while maintaining strict governance and enterprise-grade auditability.

## 2. Problem Statement
QA teams often lack structured test cases for newly demonstrated systems where formal requirements are missing. Manual test design is time-consuming, inconsistent, and prone to gaps. This system addresses these challenges by:

- Extracting **workflows** from transcripts  
- Identifying **features/modules** from slides  
- Generating **structured test scenarios and draft test cases**  
- Highlighting **ambiguities** and **coverage gaps**  
- Providing **confidence scoring** for prioritization

## 3. Assumptions & Constraints
**Assumptions:**
- Transcripts are auto-generated, moderately noisy, timestamped.
- PPT slide decks are semi-structured (feature titles present, descriptive content).
- Human-in-the-loop validation is available for Gate 1–3.

**Constraints:**
- No access to code repositories, defect databases, formal requirements, or system recordings.
- No hallucination allowed; all outputs must be grounded in transcript/slide evidence.

## 4. System Overview
**Phased Architecture:**

| Phase | Description |
|-------|-------------|
| 1     | Transcript Cleaning & Structuring |
| 2     | Slide Content Parsing & Feature Extraction |
| 3     | Workflow Extraction Engine (LLM-based + deterministic validation) |
| 4     | Feature-to-Workflow Mapping (Propose → Human Validate) |
| 5     | Test Scenario Synthesis Engine (Hybrid: positive + negative tiers) |
| 6     | Confidence & Risk Estimation Model (Composite Multi-Factor) |
| 7     | Human Review & Feedback Loop (Phase-based gates 1–3) |
| 8     | Structured Output Generator (TestRail CSV / Jira import ready) |

## 5. Data Flow Architecture
```
[Demo Transcript] --> [Transcript Normalization Layer] --> [Semantic + Timestamp Chunking] -->
[LLM Workflow Extraction Engine] --> [Deterministic Validation Layer] --> [Feature Mapping Layer] -->
[Test Scenario Synthesis Engine] --> [Confidence Engine + Coverage Intelligence] --> [Human Review Gates 1-3] -->
[Export Adapter Layer (TestRail CSV / Jira)]
[Demo PPT Slides] --> [Slide Parsing Layer + Feature Classification] --> [Feature Mapping Layer]
```

**Key Highlights:**
- **Vector Storage:** Open-source self-hosted DB (Weaviate/Qdrant)  
- **Embeddings:** Transcript chunks, slide sections, workflows, scenarios  
- **Traceability:** Multi-level (Feature → Workflow → Test Case → Step → Transcript Reference)

## 6. NLP Processing Strategy
- **Transcript Normalization:** Noise reduction, filler removal, sentence boundary reconstruction, speaker-role inference
- **Chunking:** Hybrid semantic + timestamp anchoring
- **Feature Extraction:** Slide title anchor + NLP semantic clustering of descriptive bullets
- **Workflow Extraction:** LLM-based generation with strict grounding, deterministic validation
- **Ambiguity Detection:** Semantic uncertainty scoring, REQUIRES CLARIFICATION flags

## 7. Workflow Extraction Logic
- LLM generates candidate workflows from transcript chunks  
- Guided by slide-derived features  
- Output schema:
```json
{
  "Workflow_ID": "WF-01",
  "Feature_Tag": "User Login",
  "Transcript_Reference": "T[12:35-12:48]",
  "Workflow_Steps": ["Enter credentials", "Submit form", "Handle error"],
  "Explicit_or_Implied": "Explicit",
  "Ambiguity_Flags": ["Step 2 – Expected Result REQUIRES CLARIFICATION"],
  "LLM_Confidence": 87,
  "Semantic_Grounding_Score": 0.92
}
```
- Deterministic validation ensures sequence correctness and feature alignment

## 8. Test Scenario Generation Logic
**Hybrid Layered Model:**

1. **Workflow-Level Scenarios**
   - Feature-mapped, timestamp-linked  
   - Positive + negative paths  
   - Confidence score per scenario

2. **Structured Draft Test Cases**
   - Preconditions (explicit only)  
   - Step-level actions (ordered)  
   - Expected results (transcript-supported or flagged)  
   - Test type classification (Functional / Validation / Error Handling / Navigation)  
   - Risk level based on ambiguity and complexity  
   - Negative scenario tiers (Explicit / Implied / Heuristic)

3. **Negative Scenarios**
   - Tiered: Explicit → Implied → Heuristic  
   - Heuristic negative scenarios included uniformly in confidence scoring

## 9. Confidence Scoring Model
**Multi-Factor Weighted Confidence Engine:**

| Factor                     | Weight |
|-----------------------------|--------|
| Semantic Grounding          | 25%    |
| Transcript Quality          | 15%    |
| Ambiguity Impact            | 20%    |
| Feature Mapping             | 15%    |
| Negative Tier Adjustment    | 10%    |
| Human Validation Boost      | 10%    |
| LLM Self-Score              | 5%     |

- Ambiguity severity affects scaling  
- Human validation recalibrates scores dynamically  
- Confidence propagated to scenario & step-level artifacts

## 10. Human-in-the-Loop Review Model
**Phase-Based Review Gates:**

| Gate | Purpose |
|------|---------|
| 1    | Workflow validation + feature mapping confirmation |
| 2    | Scenario validation (positive/negative tiers, completeness) |
| 3    | Draft test case validation (preconditions, steps, expected results, risk) |

- Feedback loops dynamically adjust confidence and coverage metrics  
- Ambiguities highlighted; artifacts requiring clarification flagged

## 11. Risk & Limitations
- Reliance on transcript quality and slide completeness  
- Heuristic-derived negatives may need human fine-tuning  
- High dependency on prompt design and deterministic validation rules  
- Limited domain-specific knowledge outside demo content

## 12. Implementation Roadmap
**Phase 1:** Build transcript normalization & chunking pipeline  
**Phase 2:** Implement slide parsing + feature extraction  
**Phase 3:** LLM workflow extraction + deterministic validation  
**Phase 4:** Feature-to-workflow mapping with Gate 1 review  
**Phase 5:** Scenario synthesis + negative scenario tiers  
**Phase 6:** Composite confidence engine + coverage intelligence  
**Phase 7:** Human-in-the-loop UI + Gate 2/3 workflows  
**Phase 8:** TestRail/Jira export adapters + audit logging

## 13. Future Enhancements
- ML-driven adaptive negative scenario suggestions  
- Support multi-modal inputs (screenshots, recordings)  
- Automated integration with QA dashboards for metrics tracking  
- Historical learning for improved confidence calibration

---
**End of Document**

