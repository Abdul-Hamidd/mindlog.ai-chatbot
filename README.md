# 📓 mindlog.ai: AI-Powered Journaling Companion with Grounded RAG Reflection

mindlog.ai is a **retrieval-augmented journaling application** that lets users write or speak their thoughts, tag their mood, and later ask natural-language questions about their own journal history. Every answer is generated through a hybrid retrieval pipeline and validated with automated evaluation scores, ensuring responses stay strictly grounded in the user's own words — nothing fabricated, nothing generic.

---

## 🧠 Project Architecture & Workflow

The system routes every request through a shared frontend/backend layer, then splits into two independent flows — one for saving entries, one for answering questions:

![MindLog Workflow](https://raw.githubusercontent.com/Abdul-Hamidd/mindlog-ai/main/mindlog.ai_workflow.png)

---

## 🚀 Key Technical Features

* **Hybrid Retrieval Pipeline:** Combines keyword (BM25) and semantic vector search over ChromaDB to reliably match short, informal journal entries — outperforming pure vector search alone on exact mood/date references.
* **Grounded Generation:** Answers are constrained to retrieved journal content only, preventing the LLM from filling gaps with plausible-sounding generalizations.
* **Automated Faithfulness Scoring:** Every response is scored inline via RAGAS (faithfulness + answer relevancy), giving a measurable signal of grounding rather than relying on manual review.
* **Voice-Enabled Journaling:** Users can speak their entries or questions directly through the browser's native speech recognition — no extra backend dependency required.
* **Streaming Chat Experience:** Answers, sources, and evaluation scores are streamed token-by-token to the frontend for a natural, responsive chat feel.
* **Persistent Conversation History:** Full reflection threads are saved and can be revisited, renamed via auto-generated titles, or deleted.

---

## ⚙️ Technologies & Core Stack

* **Frontend:** React (Vite), Axios for API calls, deployed on **Vercel**
* **Backend:** FastAPI, deployed via **FastAPI Cloud / SnapDeploy** (Dockerized, FastAPI + ChromaDB)
* **Vector Store:** ChromaDB (persistent local store)
* **Embeddings:** ChromaDB's built-in `ONNXMiniLM_L6_V2` (all-MiniLM-L6-v2, lightweight, torch-free)
* **LLM Inference:** Groq API
* **Evaluation Framework:** RAGAS (faithfulness, answer relevancy)
* **Database:** Supabase (Postgres) — conversations & message history
* **Voice Input:** Browser-native Web Speech API

---

## 🧩 Model & Pipeline Specifics

### 🔹 1. Retrieval Layer
Hybrid search over ChromaDB combining keyword matching and semantic similarity, using `all-MiniLM-L6-v2`-based embeddings for vectorization.

### 🔹 2. Query Rewriting
`llama-3.1-8b-instant` (via Groq) rewrites and expands the user's raw question into better retrieval queries before hitting the vector store — deliberately kept on a small, fast model to minimize added latency.

### 🔹 3. Answer Generation
`openai/gpt-oss-120b` (via Groq) generates the final grounded answer, streamed back to the frontend in real time.

> **Note:** The system originally ran on `llama-3.3-70b-versatile` via Groq. It was swapped to `openai/gpt-oss-120b` after Groq temporarily discontinued free-tier hosting for that Llama model — same Groq endpoint, different model.

### 🔹 4. Evaluation Layer
RAGAS scores every generated answer on two axes — **faithfulness** (is the answer grounded in the retrieved entries) and **answer relevancy** (does it actually address the question) — surfaced directly in the chat UI as live gauges.

---

## ⚙️ Installation & Workspace Setup

### 1. Clone the Repository
```bash
git clone https://github.com/Abdul-Hamidd/mindlog-ai.git
cd mindlog-ai
```

### 2. Backend Setup
```bash
cd backend
python -m venv venv
venv\Scripts\activate      # Windows
pip install -r requirements.txt
```

### 3. Frontend Setup
```bash
cd frontend
npm install
```

### 4. Environment Configuration
Create a `.env` file in the `backend` directory:

GROQ_API_KEY=your_groq_api_key
SUPABASE_URL=your_supabase_project_url
SUPABASE_KEY=your_supabase_service_key


Get a free Groq API key from [console.groq.com](https://console.groq.com), and your Supabase credentials from your Supabase project dashboard.

---

## ▶️ Operational Execution

**Run the backend:**
```bash
cd backend
uvicorn main:app --reload
```

**Run the frontend:**
```bash
cd frontend
npm run dev
```

The app runs with two tabs — **Write**, for journaling with text or voice, and **Reflect**, for asking grounded questions about your own entries.

---

## ⚠️ Repository Notes

Free-tier hosting environments can have ephemeral filesystems — meaning the ChromaDB vector store may not persist across every redeploy. This is a known limitation for the current demo deployment and is planned to be resolved with a managed vector database.

---

## 🌟 Strategic Roadmap

* [ ] Migrate to a managed/persistent vector database for production reliability
* [ ] Multi-user authentication and per-user data isolation
* [ ] Mobile-friendly PWA support
* [ ] Weekly/monthly mood trend analytics dashboard

---

## 👨‍💻 Author

**ABDUL HAMID**
GenAI & AI/ML Engineer
[linkedin.com/in/abdul-hamid786](https://linkedin.com/in/abdul-hamid786) | [kaggle.com/hamidai](https://www.kaggle.com/hamidai)

Give it a star ⭐ on GitHub if you find this project useful!
