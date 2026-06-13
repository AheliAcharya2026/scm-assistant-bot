# SCM Assistant — Supply Chain Governance Chatbot

A RAG (Retrieval-Augmented Generation) chatbot built on **Flowise** that answers supply chain governance questions by combining a Supplier Governance Policy PDF and a Supplier Performance CSV dataset.

---

## 🔗 Public Chatbot URL

```
https://cloud.flowiseai.com/chatbot/3bb0225d-728e-46aa-a7e8-928bda5924c5
```

> Open the link above to interact with the live SCM Assistant chatbot.

---

## 🏗️ Architecture

```
SupplyChain_Governance_Policy_v3.2.pdf
        ↓
   PDF Loader
        ↓
  Text Splitter (Recursive Character)
        ↓
    Embeddings
        ↓
  In-Memory Vector Store
        ↑
supplier_performance_data.csv
        ↓
   CSV Loader
        ↓
  Text Splitter (Recursive Character)
        ↓
    Embeddings
        ↓
  In-Memory Vector Store
        ↓
  Memory Retriever (Top K = 5)
        ↓
Conversational Retrieval QA Chain
        ↑
      LLM (command-r7b-12-2024)
        ↓
  Governance-Based Answer
```

The chatbot combines **policy rules from the PDF** with **operational supplier metrics from the CSV** to generate governance decisions. This dual-source retrieval ensures answers are grounded in both compliance requirements and real supplier performance data.

---

## ⚙️ Configuration

### LLM Used
```
command-r7b-12-2024
```

### Embeddings Used
```
Google Generative AI Embeddings — embedding-001
```

### Vector Store
```
In-Memory Vector Store (Flowise)
Top K = 5
```

### Document Store
```
Name: SCM-Policy-Store
Sources: SupplyChain_Governance_Policy_v3.2.pdf + supplier_performance_data.csv
```

---

## 📐 Chunk Configurations Tested

### Configuration 1
| Parameter | Value |
|-----------|-------|
| Chunk Size | 500 |
| Chunk Overlap | 100 |
| PDF Chunks Created |  35 |
| CSV Chunks Created | 1000 |

### Configuration 2
| Parameter | Value |
|-----------|-------|
| Chunk Size | 1000 |
| Chunk Overlap | 200 |
| PDF Chunks Created | 19 |

**Observation:** Configuration 1 (500/100) produces more granular chunks, enabling finer-grained retrieval of specific policy clauses and individual supplier records. Configuration 2 (1000/200) produces broader chunks that capture more contextual information per retrieval, useful for questions requiring cross-section reasoning.

---

## 💬 System Prompt

```
You are a Supply Chain Governance Assistant for Trinamix.

Use information from:
1. Supplier Governance Policy (PDF)
2. Supplier Performance Dataset (CSV)

Rules:
- Answer ONLY from the provided documents.
- Apply policy rules to supplier data when answering.
- Mention policy section numbers whenever relevant.
- Identify specific supplier names, tiers, and regions.
- Do not guess or make up information.
- If data is insufficient, say so clearly.
```

---

## ✅ Required Test Questions & Answers

Q1. Which Tier-3 suppliers have an active disruption flag?

Answer:

SUP-072: Regulatory enforcement pending
SUP-077: Export license risk

These suppliers are identified as Tier-3 suppliers, and the Active_Disruptions field indicates the disruption flag associated with each supplier.

Q2. Which region has highest total PO value?

Answer:

APAC (Asia-Pacific)

Zhenlong ElectroCo: PO-11162
Annual Volume Units: 1,017,400

NA (North America)

Gulf Coast Mech: 1,310,815
Ohio Valley Electronics: 271,985
Prairie Mech Corp: 1,840,574

Conclusion:

Total for NA = 1,310,815 + 271,985 + 1,840,574
North America (NA) has the highest total PO value.
Q3. Which suppliers are on SWL?

Answer:

The provided documents do not mention:

Suppliers being on SWL
Any policy rules regarding SWL

Therefore, the assistant could not determine any suppliers on SWL.

Q4. Which product category has highest average defect rate?

Answer:

Mechanical Components

Gulf Coast Mech: 2.76%
Prairie Mech Corp: 1.17%

Average:

(2.76 + 1.17) / 2 = 1.97%

Electronic Components

Ohio Valley Electronics: 1.54%
Zhenlong ElectroCo: 2.84%
Orrentek Precision Mfg: 1.44%

Average:

(1.54 + 2.84 + 1.44) / 3 = 1.94% (the screenshot states approximately 2.14%)

Conclusion (as shown in screenshot):

Electronic Components have the highest average defect rate.
Q5. Which suppliers qualify for annual Volume Rebate Program?

Answer:

The provided documents do not explicitly mention:

An annual Volume Rebate Program
Qualification criteria for such a program

Therefore, the assistant could not determine qualifying suppliers.

## 🔍 Extended Testing (Additional Questions)

Q6. Which suppliers require CAP (Corrective Action Plan)?

Answer:

The assistant identified the following suppliers with quality/compliance concerns:

Gulf Coast Mech (SUP-109)
Product Category: Mechanical Components
Defect Rate: 2.76%
Compliance Score: 84
Contract Tier: Tier-2
Risk Level: High
Certifications: ISO9001, IATF16949
Zhenlong ElectroCo (SUP-002)
Product Category: Electronic Components
Defect Rate: 2.84%
Compliance Score: 88

These suppliers were highlighted as potential candidates for corrective action.

Q7. Which suppliers violate sustainability requirements?

Answer:

Suppliers with sustainability scores below 80:

Supplier	Sustainability Score
Gulf Coast Mech (SUP-109)	82
Zhenlong ElectroCo (SUP-002)	63
Ohio Valley Electronics (SUP-111)	64

Conclusion:
The assistant suggested that low sustainability scores may indicate potential violations, but no explicit policy rule was provided to confirm violations.

Q8. Which suppliers are audit overdue?

Answer:

The documents do not provide:

Audit dates
Rules defining "audit overdue"

Therefore, the assistant could not determine overdue suppliers.

Q9. Which suppliers exceed defect thresholds?

Answer:

The documents do not define defect thresholds.

However, the highest defect-rate suppliers are:

Supplier	Defect Rate
Zhenlong ElectroCo (SUP-002)	2.84%
Gulf Coast Mech (SUP-109)	2.76%

The assistant noted these are above 2%, but could not confirm they exceed policy thresholds because none were provided.

Q10. Which suppliers require alternate supplier activation?

Answer:

The provided documents do not mention:

Suppliers requiring alternate supplier activation
Any policy rules governing alternate supplier activation

Therefore, the assistant could not determine any such suppliers.
---

## 📁 Repository Structure

```
scm-assistant-bot/
│
├── README.md                  # This file
├── scm_assistant.json         # Exported Flowise chatflow
├── .gitignore
│
└── screenshots/
   
```

---

## 🚀 How to Import and Run Locally

1. Sign up at [flowiseai.com](https://flowiseai.com)
2. Go to **Chatflows** → click **Import**
3. Upload `scm_assistant.json`
4. Add your API credentials (Gemini or OpenAI)
5. Upload the PDF and CSV to a new Document Store named `SCM-Policy-Store`
6. Click **Upsert** → then **Save** → then **Test**

---

## 🔮 Future Improvements

- **SQL-based supplier analytics** — connect a live database for real-time supplier metrics instead of static CSV
- **Dashboard visualization** — integrate charts showing supplier risk scores, defect rates, and PO values
- **Multi-agent routing** — separate agents for policy lookup vs. performance analytics with an orchestrator
- **Better query classification** — detect question type (compliance vs. performance vs. risk) and route accordingly
- **Automated audit alerts** — scheduled checks for overdue audits and SWL flagged suppliers
- **Multi-language support** — translate governance answers for global supply chain teams

---

## 👤 Author

**Aheli Acharya**  
Assignment submitted for Trinamix AI Hiring Case Study  
Date: June 2026
