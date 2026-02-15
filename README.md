# 🩺 MediRep-AI  
## AI for Healthcare & Life Sciences – AI-for-Bharat Submission

---

## 📌 Overview

**MediRep AI** is an explainable, AI-powered pharmaceutical intelligence platform designed to improve efficiency, safety, and decision-support within healthcare ecosystems.

The system consolidates fragmented drug information, interaction analysis, reimbursement lookup, and pharmacist consultation workflows into a structured, citation-backed AI assistant.

Built strictly using **publicly available and synthetic datasets**, in compliance with hackathon requirements.

---

## 🎯 Problem Statement

Healthcare professionals face:

- Fragmented drug information systems  
- High cognitive load during prescribing decisions  
- Missed drug-drug interactions  
- Limited access to structured reimbursement data  
- Language barriers in medical understanding  

These inefficiencies reduce workflow productivity and increase patient safety risks.

---

## 💡 Solution

MediRep AI provides:

- Context-aware AI medical assistant  
- 250,000+ Indian drug database  
- Real-time multi-drug interaction checker  
- Structured drug summaries with citations  
- PM-JAY reimbursement lookup  
- Verified pharmacist consultation marketplace  
- Multi-language support (10+ Indian languages)  

All responses are explainable and citation-backed.

---

## 🧠 Responsible AI Design

To align with the AI-for-Bharat Healthcare track:

- Retrieval-Augmented Generation (RAG) architecture  
- Strict citation enforcement  
- No diagnosis or prescriptive outputs  
- Mandatory medical disclaimers  
- Human escalation via pharmacist consultation  
- No storage of sensitive patient medical records  
- Synthetic/public dataset usage only  
- Mode-based boundary enforcement  

---

## 📂 Repository Structure

```
AI-for-Bharat/
│
├── requirements.md   # Generated via Kiro Spec Flow
├── design.md         # Generated via Kiro Design Flow
├── README.md
```
---

## 📑 Artefacts Generated via Kiro

This project uses **Kiro’s “Spec → Design” workflow** to convert the natural language idea into structured system artefacts:

- `requirements.md`  
  - Functional & non-functional requirements  
  - Responsible AI compliance  
  - Performance, scalability, and security criteria  

- `design.md`  
  - System architecture  
  - RAG pipeline  
  - Database structure  
  - API integrations  
  - Deployment and scalability design  

Both files were generated and iteratively refined using Kiro.

---

## 🏗 Architecture Overview

- **Frontend:** Next.js + TypeScript  
- **Backend:** FastAPI (Python)  
- **LLM:** Google Gemini API (Primary), Groq (Fallback)  
- **Vector Database:** Qdrant  
- **Structured Database:** Turso (LibSQL) + Supabase  
- **Payments:** Razorpay  
- **Voice/Video:** Agora  
- **External Data Sources:** OpenFDA, RxNorm, PubChem  

The system uses a RAG-based pipeline to ensure grounded, citation-backed responses.

---

## 📊 Success Metrics

- 95%+ drug information accuracy  
- 90%+ responses include verified citations  
- <5 second P95 response time  
- 80%+ search relevance (top 5 results)  
- 99.5% system uptime target  

---

## 🔒 Compliance & Constraints

- Informational tool only (not a medical device)  
- No real patient medical record storage  
- India-based data residency  
- Encryption in transit (TLS 1.3) and at rest (AES-256)  
- JWT-based authentication with row-level security  

---

## 🚀 Future Enhancements (Post-MVP)

- EHR integration  
- Clinical trial matching  
- Medication adherence tracking  
- Direct insurance claim filing  
- Pharmacy delivery network integration  

---

## 📌 Track Alignment

MediRep AI directly supports:

✔ Clinical information summarization  
✔ Workflow automation  
✔ Decision-support systems  
✔ Reimbursement navigation  
✔ Responsible AI healthcare augmentation  

---

## 🏁 Submission Deliverables

- GitHub repository containing `requirements.md` and `design.md`  
- Presentation deck (PDF format)  
- Kiro-generated specification artefacts  

---

## ⚖ Disclaimer

MediRep AI provides informational support based on publicly available medical data.  
It does not provide diagnosis, prescribe treatment, or replace licensed medical professionals.

---

**MediRep AI transforms fragmented pharmaceutical data into structured, explainable, real-time decision support for healthcare professionals in India.**
