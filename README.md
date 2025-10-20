# 🔹 BABOK + RAG + Gemini-Based Business Requirement Chatbot

**(AI-Powered Business Analysis Assistant)**

---

## 🎯 Project Objective

This project aims to **automate the definition, classification, and prioritization of business requirements** in the business analysis process.
Traditionally, documenting and analyzing requirements takes hours or days — this chatbot reduces it to **a few seconds** while ensuring compliance with international standards.

### System Overview

- 🧠 **Data-Driven (RAG-Powered)** — Retrieves and analyzes semantically similar requirements from a vector database.
- 📘 **BABOK-Compliant** — Generates requirement documents aligned with the Business Analysis Body of Knowledge (BABOK).
- ⚙️ **Scalable (ChromaDB + Gemini)** — Designed for high scalability and efficient retrieval on large datasets.
- 💬 **User-Friendly (Gradio Interface)** — Simple and interactive interface for analysts and business users.

In essence, this chatbot partially automates a business analyst’s key tasks:

* Classifies requirements as **Functional** or **Non-Functional**.
* Generates structured **BABOK-compliant requirement documentation**.
* Calculates **RICE** and **WSJF** priority scores.
* Displays results in an intuitive **Gradio web interface**.

---

## 🧩 System Architecture

![architecture](Business%20Requirement%20Chatbot.png)

> 💡 **Data Flow:**
> User → Embedding (Gemini) → ChromaDB (RAG) → LLM (Gemini) → BABOK-Compliant Output → Prioritization (RICE / WSJF) → Gradio UI

| Layer                  | Technology                      | Description                                                           |
| :--------------------- | :------------------------------ | :-------------------------------------------------------------------- |
| 🤖 **LLM**             | Google Gemini (1.5 Flash / Pro) | Analyzes requirements and generates BABOK-compliant content.          |
| 📊 **Database**        | ChromaDB                        | Stores vectorized requirements for semantic similarity search.        |
| 🧠 **Embedding Model** | `text-embedding-004`            | Converts text into numerical vector representations.                  |
| 📚 **Dataset**         | PURE Annotate Dataset (Kaggle)  | Contains labeled requirement sentences (Functional / Non-Functional). |
| ⚖️ **Prioritization**  | RICE & WSJF                     | Scores and ranks requirements based on business impact and urgency.   |
| 💬 **Web Interface**   | Gradio                          | Interactive interface for real-time user input and output.            |

---

## ⚙️ Installation & Environment Setup

### 1️⃣ Install Dependencies

```bash
pip install -q chromadb sentence-transformers google-generativeai python-dotenv gradio pandas
pip install -q transformers==4.41.2 huggingface_hub==0.22.2
```

### 2️⃣ Configure API Key

Create a `.env` file and add your Google Gemini API key:

```bash
GOOGLE_API_KEY="your_api_key_here"
```

### 3️⃣ Run the Project

```bash
python app.py
```

💡 The Gradio web interface will launch automatically in your browser.

---

## 📊 Dataset — PURE Annotate Dataset

The project uses the **Public Requirements PURE Dataset** from Kaggle.
Each row represents a unique business requirement statement:

| Type           | Example                                   |
| -------------- | ----------------------------------------- |
| Functional     | “System shall export reports in PDF.”     |
| Non-Functional | “System shall encrypt user data at rest.” |

### Labeling Logic

* `NFR_boolean = 0` → Functional Requirement
* `NFR_boolean = 1` → Non-Functional Requirement

If the dataset does not include these labels, the system applies **heuristic analysis** based on key terms such as *security, performance, compliance,* etc.

**Examples:**

* “security”, “encryption”, “performance” → **Non-Functional**
* “save”, “create”, “list” → **Functional**

---

## 🔡 Embedding & Knowledge Base (ChromaDB)

Each requirement statement is embedded into a numerical vector using **Gemini’s embedding model**, then stored in **ChromaDB**, enabling semantic search.

```python
emb = genai.embed_content(model="models/text-embedding-004", content=text)
collection.add(ids, documents, embeddings)
```

💡 This allows the chatbot to find semantically similar requirements when analyzing new user inputs.

---

## 🧠 BABOK-Compliant Requirement Generation

The model outputs requirements following **BABOK (Business Analysis Body of Knowledge)** standards, populating structured fields:

| Field                      | Description                                                        |
| -------------------------- | ------------------------------------------------------------------ |
| **Requirement Type**       | Business / Stakeholder / Solution / Transition                     |
| **Nature (F/NF)**          | Specifies whether the requirement is Functional or Non-Functional. |
| **Rationale**              | Explains the reason or justification.                              |
| **Business Value**         | Defines the benefit or impact on business outcomes.                |
| **Acceptance Criteria**    | Defines measurable validation criteria.                            |
| **MoSCoW Priority**        | Must / Should / Could / Won’t                                      |
| **Impact / Effort / Risk** | Rated 1–5 to support prioritization.                               |
| **Kano Category**          | Basic / Performance / Excitement                                   |
| **Cost of Delay**          | Describes financial or operational consequences of delay.          |

> All outputs are generated in **structured, measurable, and traceable format** to align with BABOK standards.

---

## ⚖️ Requirement Prioritization Module

The chatbot automatically calculates requirement priorities using two industry-standard models:

| Metric   | Formula                                                         | Purpose                                        |
| -------- | --------------------------------------------------------------- | ---------------------------------------------- |
| **RICE** | (Reach × Impact × Confidence) / Effort                          | Measures user impact and development effort.   |
| **WSJF** | (Business Value + Time Criticality + Risk Reduction) / Job Size | Optimizes delivery order by value and urgency. |

### Automated Signal Detection

* “security”, “encryption”, “compliance” → High Risk
* “performance”, “scalability” → High Impact
* “availability”, “backup”, “HA” → Time Critical

**Example Output:**

```
🧮 Suggested Prioritization
- RICE: 46.6
- WSJF: 4.2
- Risk: 5
- Impact: 4
🏷️ Requirement Nature: Non-Functional
```

---

## 🔍 RAG (Retrieval-Augmented Generation) Workflow

1. **Retrieval** — User query is embedded and semantically matched with similar requirements in ChromaDB.
2. **Augmentation** — Retrieved examples are added to the model’s context.
3. **Generation** — Gemini produces structured BABOK-aligned requirements.

💡 This approach enables **data-grounded** and **context-aware** generation for accurate and explainable results.

---

## 💬 Gradio Interface

Gradio provides a lightweight and interactive web interface for requirement generation.
Users simply enter a project description, and the chatbot returns BABOK-compliant requirements.

```bash
python app.py
```

**Example:**

```
💬 Project Description:
"The banking app must ensure customer data privacy and maintain high transaction performance."

📘 Output:
- Requirement Type: Solution
- Nature: Non-Functional
- Requirement: The system shall encrypt customer data using AES-256.
- Rationale: To ensure compliance with PCI-DSS and data protection regulations.
```

---

## 🧪 Test Example

```python
test_query = "The system must handle 5 million API calls daily with 99.9% success rate."
print(rag_response_babok(test_query))
```

📈 **Sample Output:**

🚀 TEST RUNNING...
📝 Query: The system must process 5 million API calls per day with 99.9% success rate.

📘 Example Output:

Based on the user’s project description, here are BABOK-compliant requirement suggestions generated by the model:

────────────────────────────
**Requirement Proposal 1**

**Type:** Solution  
**Nature (F/NF):** Non-Functional  
**Requirement:** The system must handle at least 5 million API calls per day with a 99.9% success rate.  
**Rationale:** Defines the expected performance level and service reliability based on the stated success threshold.  
**Business Value:** Ensures operational continuity, enhances reliability, and improves customer satisfaction.  
**Stakeholders:** End Users, Business Team, System Administrators, Development Team, Support Team  
**Acceptance Criteria:**
- Under load testing, the system should successfully process at least 5 million API calls within 24 hours.  
- Success rate must be ≥ 99.9%.  
- Failed calls must be logged with root cause details.  
- Tests should simulate real-world usage scenarios.  
**MoSCoW:** Must (critical for reliability and uptime)  
**Impact:** 5 **Effort:** 4 **Risk:** 3  
**Kano Class:** Performance (Higher reliability → higher satisfaction)  
**Cost of Delay:** High — service downtime may cause revenue and trust loss.  

────────────────────────────
**Requirement Proposal 2**

**Type:** Solution  
**Nature (F/NF):** Non-Functional  
**Requirement:** The system shall maintain an average API response time below 200 milliseconds.  
**Rationale:** Low latency improves user experience and overall system performance.  
**Business Value:** Increases engagement, improves competitiveness, and enhances satisfaction.  
**Stakeholders:** Users, Business, System Administrators, Developers  
**Acceptance Criteria:**
- Random API samples tested for 1 hour must have an average latency < 200 ms.  
- Peak latency must remain below 500 ms.  
- Tests repeated under different load levels (e.g., 1000 RPS, 5000 RPS).  
**MoSCoW:** Should (important for performance)  
**Impact:** 4 **Effort:** 3 **Risk:** 2  
**Kano Class:** Performance  
**Cost of Delay:** Medium — slow responses degrade UX and adoption.  

────────────────────────────
**Requirement Proposal 3**

**Type:** Solution  
**Nature (F/NF):** Non-Functional  
**Requirement:** The system shall support horizontal scalability to handle future load increases.  
**Rationale:** Ensures adaptability to future growth and increased demand.  
**Business Value:** Enables business scalability, improves resilience, and reduces long-term costs.  
**Stakeholders:** Business, System Administrators, Development Team  
**Acceptance Criteria:**
- Adding servers or resources should scale performance linearly without manual reconfiguration.  
- Scaling should occur with <5 minutes downtime.  
- Load tests at 5M, 10M API calls should show expected performance growth.  
**MoSCoW:** Should (important for sustainability)  
**Impact:** 4 **Effort:** 4 **Risk:** 3  
**Kano Class:** Performance  
**Cost of Delay:** Medium — limits business scalability.  

────────────────────────────
**Requirement Proposal 4**

**Type:** Solution  
**Nature (F/NF):** Non-Functional  
**Requirement:** The system must generate detailed and meaningful error messages and log them.  
**Rationale:** Improves incident resolution time and system stability.  
**Business Value:** Reduces downtime, maintenance cost, and builds user trust.  
**Stakeholders:** Dev Team, Support Team, Admins  
**Acceptance Criteria:**
- All error messages must contain cause and possible resolution hints.  
- Messages must be clear and user-friendly.  
- All errors logged with timestamps and source information.  
- Logs stored centrally and must be easily searchable.  
- Adjustable logging levels (DEBUG, INFO, WARNING, ERROR, FATAL).  
**MoSCoW:** Must (essential for monitoring and maintenance)  
**Impact:** 5 **Effort:** 2 **Risk:** 1  
**Kano Class:** Basic (expected by users)  
**Cost of Delay:** High — unlogged errors may cause severe service loss.  

────────────────────────────
**Requirement Proposal 5**

**Type:** Solution  
**Nature (F/NF):** Functional  
**Requirement:** The system shall provide real-time monitoring and reporting of API usage metrics (calls, latency, error rate, etc.).  
**Rationale:** Enables continuous performance tracking and early anomaly detection.  
**Business Value:** Supports optimization, proactive risk management, and informed decision-making.  
**Stakeholders:** Business, Admins, Developers, Security Team  
**Acceptance Criteria:**
- The system must track metrics in real time (e.g., every 1 minute).  
- Display data as interactive charts and tables.  
- Send alerts when thresholds are exceeded (e.g., via email/SMS).  
- Generate reports for different time ranges (daily, weekly, monthly).  
- Store data securely and prevent unauthorized access.  
**MoSCoW:** Should (important for performance monitoring)  
**Impact:** 4 **Effort:** 3 **Risk:** 2  
**Kano Class:** Performance (better monitoring → higher reliability)  
**Cost of Delay:** Medium — delayed detection can lead to costly outages.  

---

## 🧩 Project Structure

```
📦 business-requirement-chatbot/
 ┣ 📂 data/
 ┃ ┗ Pure_Annotate_Dataset.csv
 ┣ 📂 src/
 ┃ ┣ embeddings.py
 ┃ ┣ generator.py
 ┃ ┣ prioritization.py
 ┃ ┗ ui.py
 ┣ app.py
 ┣ .env.example
 ┗ README.md
```

---

## 📘 License & Contributions

This project is released under the **MIT License**.


## 🧠 Results & Evaluation

This project demonstrates that AI-driven requirement engineering can effectively operationalize the principles of the BABOK (Business Analysis Body of Knowledge) framework.

Traditionally manual and time-intensive processes such as requirement identification, classification, and prioritization have been streamlined through a combination of LLM-based automation and data-driven analysis.

Key Findings

- The RAG (Retrieval-Augmented Generation) architecture enables not only textual but also contextual and semantic-level analysis of requirements.
- The Gemini LLM successfully transforms user-provided natural language project descriptions into BABOK-compliant requirement structures.
- The generated outputs align with Business, Stakeholder, Solution, and Transition requirement types and maintain full traceability and consistency.

Added Value

This system enhances the discipline of business analysis by:

- Improving requirement quality, traceability, and consistency,
- Supporting data-driven prioritization and decision-making,
- Enabling scalable and reusable organizational knowledge bases.
- Academic and Industrial Perspective
- 
By bridging requirement engineering and LLM-based knowledge management, this project lays the groundwork for future research and development in:

- Automated requirement validation,
- Consistency and dependency analysis,
- Enterprise knowledge base integration.

In conclusion, the integration of BABOK principles, RAG methodology, and the Gemini LLM demonstrates that artificial intelligence can serve as a strategic partner in business analysis, enhancing both efficiency and analytical depth in requirement engineering.

---
