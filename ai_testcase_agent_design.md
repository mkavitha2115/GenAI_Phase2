# AI Workflow & Test Scenario Generator Agent – Technical Design Document

**Author:** Kavitha Shanmugam  
**Role:** Chief AI Agentic Architect  
**Date:** 27-Feb-2026  

---

## 1. Objective

Design a **production-grade AI agent** to automatically generate **high-level workflows and structured test cases** from demo transcripts and PPT slide decks, with **human-in-the-loop validation** to ensure traceability, minimal hallucination, and review-ready outputs.

**Primary Goals:**

- Balanced coverage, traceability, and low hallucination
- Human-in-the-loop oversight at all phases
- Structured, traceable, and auditable outputs
- Integration with existing test cases

---

## 2. Input Sources

| Input Type               | Embedding Strategy | Notes |
|---------------------------|-----------------|-------|
| Demo Transcripts          | Hybrid (Full + Chunked) | Full transcript embedding for global context; chunked embedding for precise traceability |
| Demo PPT Slide Decks      | Section-level embedding | Slides grouped by feature/module and embedded as blocks |
| Partial code/PR references (PV) | Ignored | Only full vectors (V) used for embedding |

**Assumptions:**  
- No access to direct recordings  
- No formal requirements documentation available  

---

## 3. Output Requirements

### 3.1 High-Level Test Scenarios

- Feature-mapped workflows  
- Module-tagged workflows  
- Hybrid granularity: module-level structure + step-level critical steps  
- Positive and negative paths: positive paths fully generated; negative paths only when high-confidence  
- Traceable to transcript timestamps, PPT slide sections, and inferred workflow IDs  

### 3.2 Structured Draft Test Cases

- **Mandatory Fields:** Preconditions, Test Steps, Expected Results, Test Type, Risk Level, AI Confidence Score, Traceability references  
- **Test Type Classification:** Hybrid — rule-based mapping first, AI classification for ambiguous cases  
- **Risk Level Assignment:** Hybrid — rule-based default with AI refinement for critical workflows  
- **Knowledge Gaps:** Documented both inline and in a structured table with workflow ID, gap description, confidence score, suggested action  

### 3.3 Knowledge Gaps & Coverage Gaps

- Gaps flagged for human review  
- Coverage gaps identified by comparing generated + existing tests vs. demo content, supplemented by AI inference where confidence is high  

### 3.4 Integration with Existing Tests

- Compare generated tests with existing ones  
- Flag conflicts or affected tests  
- Suggest modifications for human approval  

---

## 4. Architecture Overview

### 4.1 Vector Embedding Storage

- Dedicated **vector database** (e.g., Pinecone, Weaviate, Milvus) for embeddings  
- Efficient similarity search for transcripts and PPTs  
- Metadata stored alongside vectors (workflow ID, module, feature)  

### 4.2 Phased Execution Flow

1. **Input Preprocessing & Embedding**  
   - Text extraction, chunking, and vectorization of transcripts and PPT sections  
   - Human review optional at this phase  

2. **Workflow Extraction**  
   - Hybrid granularity: module-level + step-level  
   - Positive/negative paths generated per confidence thresholds  
   - Human review integrated  

3. **Knowledge & Coverage Gap Detection**  
   - Ambiguous workflows flagged  
   - Missing validation criteria identified  
   - Coverage gaps inferred using AI based on embeddings  
   - Human review integrated  

4. **Test Case Generation**  
   - Structured draft test cases with mandatory fields  
   - Traceability maintained  
   - Human review integrated  

5. **Review-Ready Packaging**  
   - Inline notes + structured tables for knowledge gaps  
   - Export-ready formats for human sign-off  

---

## 5. AI Confidence & Human-in-the-Loop

- **AI Confidence Scoring:** Hybrid — weighted evidence + semantic similarity + human validation feedback  
- **Human-in-the-loop:** Integrated at all phases for validation, feedback, and continuous improvement  
- **Continuous Learning:** Human feedback updates embeddings, inference heuristics, and scoring dynamically  

---

## 6. System Features

- **Traceability:** Transcript timestamps + PPT sections + workflow IDs  
- **Review-Ready Outputs:** Fully structured test cases, inline notes, and gap tables  
- **Coverage Analysis:** Highlights untested scenarios and areas needing clarification  
- **Existing Test Integration:** Suggests modifications while preserving auditability  

---

## 7. Technical Stack Recommendations

| Component                  | Recommendation |
|-----------------------------|----------------|
| Vector Database             | Pinecone / Weaviate / Milvus |
| Storage for metadata & outputs | Relational DB (optional) |
| NLP & Embedding Model       | LLM with vector embedding capability (e.g., OpenAI embeddings or Hugging Face) |
| Workflow Inference Engine   | Python-based pipeline leveraging semantic similarity + rules |
| Human Review Interface      | Web or dashboard interface to approve/modify workflows, test cases, gaps, and coverage |
| Continuous Learning Module  | Feedback loop to update embeddings, heuristics, and confidence scores |

---

## 8. Summary

This agent design ensures:

- **Balanced objectives**: coverage, traceability, low hallucination  
- **Structured, traceable, review-ready outputs**  
- **Human-in-the-loop** integrated at all phases  
- **Modular, multi-phase architecture** enabling scalable and maintainable test generation  
- **Continuous learning** from human feedback for ongoing improvement