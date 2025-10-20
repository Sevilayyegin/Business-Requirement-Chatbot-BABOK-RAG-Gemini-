# 🔹 BABOK + RAG + Gemini-Based Business Requirement Chatbot

**(AI-Powered Business Analysis Assistant)**

---

## 🎯 Project Objective

This project aims to **automate the definition, classification, and prioritization of business requirements** in the business analysis process.
Traditionally, documenting and analyzing requirements takes hours or days — this chatbot reduces it to **a few seconds** while ensuring compliance with international standards.

### System Overview

🧠 **Data-Driven (RAG-Powered)** — Retrieves and analyzes semantically similar requirements from a vector database.
📘 **BABOK-Compliant** — Generates requirement documents aligned with the Business Analysis Body of Knowledge (BABOK).
⚙️ **Scalable (ChromaDB + Gemini)** — Designed for high scalability and efficient retrieval on large datasets.
💬 **User-Friendly (Gradio Interface)** — Simple and interactive interface for analysts and business users.

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

* Nature: Non-Functional
* RICE Score: 44.8
* WSJF Score: 5.1
* BABOK Output: “The system shall process 60,000 requests per second with under 200ms latency.”

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

This project successfully demonstrates how **AI-driven requirement engineering** can automate business analysis processes following **BABOK** principles.

Thanks to the RAG architecture, the chatbot produces:

* Contextually relevant, semantically aligned requirements,
* Consistent and traceable outputs,
* Structured, measurable deliverables.

The model integrates **Gemini LLM** with **vector-based retrieval**, forming a scalable and reliable foundation for future applications in:

* Automated business analysis,
* Requirement quality assessment,
* Enterprise knowledge management.

---
