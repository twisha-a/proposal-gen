# Proposal Generator — Architecture & Workflow (with Embeddings)

## Overview
This system generates federal proposal documents from SAM.gov contract data and supporting RFP documents.

- Input: CSV + RFP attachments  
- Output: Proposal (DOCX + JSON)  
- LLM: qwen3:8b (Ollama)  
- Enhancement: Semantic Retrieval using Embeddings  

---

## Inputs
- SAM.gov CSV row  
- RFP / Solicitation documents  
- Supporting files (SOW, Evaluation Criteria, Past Performance, Pricing, etc.)  

If files are missing, system falls back to available data or CSV metadata.

---

## Architecture

### Before
CSV → Parser → Attachments → Context (60k chars) → LLM → Proposal

### After (Embeddings)
CSV → Parser → Attachments → Chunk + Embed → Retriever → LLM → Proposal

---

## Components

### main.py
- Orchestrates pipeline
- Modes: standard, embedding, both

### attachment_reader.py
- Extracts text from documents
- Prioritizes SOW, Eval, Past Perf, Pricing

### expander.py
- Extracts structured scope via LLM:
  - deliverables
  - phases
  - technical_requirements
  - risks
  - staffing

### knowledge.py
- Injects compliance and contract guidance

---

## Retriever (NEW)

### Flow
Documents → chunk (~400 words) → embed → store vectors  
Query → embed → cosine similarity → top-k chunks  

### Benefits
- Reduces context from 60k → ~3k
- Filters irrelevant content
- Handles synonyms and domain variation
- Enables section-specific context

---

## Generator

- 8 sections generated via LLM
- Each section uses:
  - contract metadata
  - scope
  - compliance guidance
  - retrieved context (embedding mode)

---

## Workflow

1. Parse CSV  
2. Read attachments  
3. Extract scope  
4. (NEW) Chunk + Embed + Retrieve  
5. Generate sections  
6. Deduplicate  
7. Export (JSON + DOCX)  

---

## Modes

| Mode | Description |
|------|------------|
| standard | Full raw context |
| embedding | Focused semantic retrieval |
| both | Compare outputs |

---

## Trade-offs

### Pros
- Higher relevance
- Reduced hallucination
- Works across domains

### Cons
- Slightly slower
- Requires embedding model
- Depends on Ollama

---

## Final Architecture

CSV → Parser → Attachments → Retriever → Generator → Exporter
                 ┌──────────────┐
                 │   CSV Input  │
                 └──────┬───────┘
                        ↓
                 ┌──────────────┐
                 │   Parser     │
                 └──────┬───────┘
                        ↓
                 ┌──────────────┐
                 │ Attachments  │
                 └──────┬───────┘
                        ↓
          ┌────────────────────────────┐
          │ Retriever (NEW)            │
          │ Chunk + Embed + Retrieve   │
          └──────┬─────────────────────┘
                 ↓
          ┌──────────────┐
          │ Generator    │
          └──────┬───────┘
                 ↓
          ┌──────────────┐
          │ Exporter     │
          └──────────────┘
---

## Key Takeaway

Embeddings transform the pipeline from document-level generation to section-level intelligent retrieval.
