# 🗺️ CareMap

### Your bridge to better healthcare across India

> *In a country of 1.4 billion, near enough is not good enough.*

CareMap is an **Agentic Healthcare Intelligence System** that transforms 10,000+ messy, unstructured Indian medical facility records into a trusted, searchable healthcare discovery network — powered by **CareGuide**, our AI reasoning agent.

Built in 24 hours for **Hack-Nation 2026 × Databricks** (Challenge 03: Serving A Nation).

---

## The Problem

70% of India's population lives in rural areas where finding the right hospital is a fragmented puzzle. Patients travel hours only to discover a facility lacks the surgeon, equipment, or specialist they urgently need.

This is not a hospital shortage — it's a **discovery and verification crisis**.

---

## What CareMap Does

| Feature | Description |
|---|---|
| **Smart Discovery** | Natural language search across 10K facilities using 1024-dim vector embeddings |
| **Trust Verification** | 6 contradiction rules automatically flag unreliable facility claims |
| **3-Agent Reasoning** | Query Agent → Search Agent → CareGuide Validator with full chain of thought |
| **Medical Desert Mapping** | PIN-code level analysis of healthcare coverage gaps across India |

---

## How It Works

```
User: "Find emergency surgery facility in rural Bihar"
                    │
                    ▼
           ┌─────────────────┐
           │   Query Agent   │  ← Parses intent, extracts filters
           │  (Llama 3.3 70B)│     state=Bihar, caps=[emergency, surgery]
           └────────┬────────┘
                    │
                    ▼
           ┌─────────────────┐
           │  Search Agent   │  ← Vector search + SQL filters
           │ (BGE-Large 1024)│     across 10K embedded facility records
           └────────┬────────┘
                    │
                    ▼
           ┌─────────────────┐
           │   CareGuide     │  ← Validates, ranks, generates
           │  (Llama 3.3 70B)│     warm personalized recommendation
           └────────┬────────┘
                    │
                    ▼
  "I recommend Jalal Medical Center in Motihari.
   They offer 24x7 emergency services including
   surgery. I'd avoid the dental clinic as it's
   not equipped for appendectomy."
              — CareGuide, by CareMap
```

---

## Trust Scoring Engine

Since there is no ground truth, we built our own verification layer. Each facility starts at 100 points and is penalized for contradictions:

| Rule | Risk | Deduction |
|---|---|---|
| Surgery capability claimed but no surgeon/anesthesiologist listed | HIGH | -25 |
| ICU claimed but no ventilator or cardiac monitor | HIGH | -25 |
| Emergency services claimed but no blood bank, oxygen, or ambulance | HIGH | -25 |
| 24/7 availability claimed but only part-time/visiting doctors | MEDIUM | -15 |
| Advanced specialty listed but no specialist doctor and zero doctor count | MEDIUM | -15 |
| Clinic type claiming 50+ beds | LOW | -5 |

**Results across 10,000 facilities:**
- 🟢 9,320 GREEN (75–100)
- 🟡 547 YELLOW (40–74)
- 🔴 133 RED (0–39)
- Average trust score: **91.6/100**

---

## Tech Stack

| Component | Technology |
|---|---|
| **Compute** | Databricks Free Edition (Serverless) |
| **Data Store** | Delta Lake + Unity Catalog |
| **Embeddings** | databricks-bge-large-en (1024 dimensions) |
| **LLM** | Llama 3.3 70B Instruct (Databricks-hosted) |
| **Vector Search** | Mosaic AI Vector Search |
| **Observability** | MLflow 3 Tracing |
| **Frontend** | React |
| **API** | MLflow Model Serving |

---

## Project Structure

```
CareMap/
├── README.md
├── presentation/
│   └── CareMap_Pitch.pptx
├── databricks-notebooks/
│   ├── 01_Data_Pipeline_Healthcare.ipynb    # Data ingestion + cleaning
│   ├── 02_Trust_Scoring.ipynb               # 6-rule trust engine
│   ├── 03_Vector_Search.ipynb               # Embeddings + vector index
│   └── 04_Agentic_Chain.ipynb               # 3-agent CareGuide pipeline
├── frontend/
│   ├── src/
│   ├── public/
│   ├── package.json
│   └── caremap_demo_responses.json
└── data/
    └── sample_response.json
```

---

## Setup & Reproduction

### Databricks Backend

1. Create a free Databricks workspace at [databricks.com](https://databricks.com)
2. Upload `VF_Hackathon_Dataset_India_Large.xlsx` to your workspace
3. Import and run notebooks in order: `01` → `02` → `03` → `04`
4. The pipeline creates:
   - A cleaned Delta table with 10K facilities
   - Trust scores for all facilities
   - A Vector Search index for semantic retrieval
   - A deployed CareGuide API endpoint

### Frontend

```bash
cd frontend
npm install
npm start
```

The React app connects to the Databricks Model Serving endpoint. A fallback to pre-computed responses (`caremap_demo_responses.json`) is included for offline demos.

---

## Sample API Response

```json
{
  "question": "Find emergency surgery facility in Bihar",
  "careguide_message": "I recommend Jalal Medical Center in Motihari. They offer 24x7 emergency services including surgery. — CareGuide, by CareMap",
  "emergency_note": "Call 108 immediately if life-threatening",
  "candidates_found": 4,
  "facility_cards": [
    {
      "rank": 1,
      "facility_name": "Jalal Medical Center",
      "city": "Motihari",
      "state": "Bihar",
      "verdict": "VERIFIED & RECOMMENDED",
      "what_they_offer": "24x7 emergency services including surgery",
      "concerns": "Verified — no issues found"
    }
  ],
  "chain_of_thought": [
    "Filtered facilities for emergency + surgery capability in Bihar",
    "Eliminated dental and sports injury clinics as unsuitable",
    "Verified trust scores and equipment flags",
    "Ranked by relevance and reliability"
  ]
}
```

---

## Key Numbers

| Metric | Value |
|---|---|
| Facilities processed | 10,000 |
| Surgery claims flagged (missing surgeon) | 1,844 |
| ICU facilities identified | 339 |
| PIN code regions analyzed | 427 |
| Embedding dimensions | 1,024 |
| Trust score rules | 6 |
| Average response time | ~8 seconds |

---

## Team

| Name | Role | Focus Area |
|---|---|---|
| **Babar** | AI/ML Lead | LLM extraction, RAG pipeline, agentic chain |
| **Umar** | Data Engineer | Databricks setup, data pipeline, vector search |
| **Taseer** | Full-Stack Lead | React frontend, API integration, UX |
| **Swaeba** | Support Engineer | Map visualization, pitch deck, UI support |

---

## Evaluation Alignment

| Criteria | Weight | How CareMap Addresses It |
|---|---|---|
| Discovery & Verification | 35% | 3-agent chain with trust scoring across 10K rows |
| Intelligent Document Parsing | 30% | LLM extraction from unstructured notes + vector embeddings |
| Social Impact & Utility | 25% | Medical desert detection, NGO-actionable insights |
| User Experience & Transparency | 10% | CareGuide warm responses + full chain-of-thought |

---

## License

Built for Hack-Nation 2026 × World Bank Youth Summit × Databricks.

---

<p align="center">
  <strong>🗺️ CareMap — Because no family should travel hours to find the wrong hospital.</strong>
</p>
