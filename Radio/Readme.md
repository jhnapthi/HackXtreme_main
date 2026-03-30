# GlobalSentry

**GlobalSentry** is a unified, multi-agent RAG threat intelligence platform — a hackathon project for HackExtreme.

It monitors three global threat domains in real-time, cross-references them for cascading risks, and alerts stakeholders before crises hit mainstream news.

---

## 🧠 Architecture — LangGraph Multi-Agent Pipeline

```
Profiler → Triage → [Retriever → Analyst → Correlator → Validator] → Notify → Archiver
```

| Node | Role | Description |
|---|---|---|
| **Profiler** | The Personalizer | Scores relevance of the event to the stakeholder profile |
| **Triage** | The Sentry | Fast, mode-aware YES/NO threat classifier (Agent A) |
| **Retriever** | The Historian | Fetches same-mode historical context from Qdrant |
| **Analyst** | The Brain | Deep domain-expert analysis with severity + confidence score (Agent B) |
| **Correlator** | 🧠 The Neural Moat | Queries Qdrant for **cross-mode** correlations — detects cascading threats |
| **Validator** | The Fact-Checker | Verifies claims via live DuckDuckGo search (Agent C) |
| **Notifier** | The Messenger | Sends formatted Telegram alert |
| **Archiver** | The Memory | Stores event in Qdrant with mode + severity metadata |

---

## 🌍 Three Sentry Modes (SDG Aligned)

| Mode | SDG | Focus |
|---|---|---|
| 🩺 **Epi-Sentry** | SDG 3 | Disease outbreaks, epidemics, public health emergencies |
| 🌪️ **Eco-Sentry** | SDG 11 / 13 | Climate disasters, extreme weather, ecological collapse |
| ♻️ **Supply-Sentry** | SDG 12 | Supply chain disruptions, ESG violations, resource shortages |

---

## 🔑 Key Files

| File | Purpose |
|---|---|
| `sentry.py` | Core LangGraph engine — all 8 nodes + graph wiring |
| `ingest.py` | Mode-aware polling daemon — reads RSS feeds, deduplicates, triggers pipeline |
| `seed_data.py` | Pre-loads 18 demo events into Qdrant for Retriever + Correlator |
| `user_profile.json` | Stakeholder profile — region, role, sentry mode, alert threshold |
| `.env` | API keys + Ollama config + per-mode RSS feeds |

---

## 🚀 Setup

### Prerequisites
- Python 3.9+
- [Ollama](https://ollama.ai/) running locally with a model pulled (e.g. `llama3`)
- Telegram Bot token (optional, for alerts)

### 1. Pull Ollama model
```bash
ollama pull llama3
```

### 2. Install dependencies
```bash
pip install -r requirements.txt
```

### 3. Configure environment
```bash
cp .env.template .env
# Edit .env — set SENTRY_MODE, RSS feeds, Telegram token
```

### 4. Customise stakeholder profile
Edit `user_profile.json`:
```json
{
  "stakeholder_type": "government_planner",
  "region_of_interest": "South Asia",
  "active_sentry_mode": "eco",
  "alert_threshold": 0.5
}
```

### 5. Seed demo data (run once)
```bash
python seed_data.py
```

### 6. Run the monitoring daemon
```bash
python ingest.py
```

---

## 🎮 Demo Inputs (Hackathon)

Use these headlines to guarantee a triggered alert during the live demo:

| Mode | Headline |
|---|---|
| 🩺 Epi | `New respiratory illness cluster confirmed in Southeast Asia — WHO monitoring` |
| 🌪️ Eco | `Magnitude 7.1 earthquake strikes coastal Peru — tsunami watch issued` |
| ♻️ Supply | `Major semiconductor factory halts operations — global chip shortage feared` |

---

## ⚙️ LLM & Embedding Configuration

GlobalSentry runs **100% locally** — no cloud AI costs:

| Component | Default | Config |
|---|---|---|
| LLM (all agents) | `llama3` via Ollama | `OLLAMA_MODEL` in `.env` |
| Embeddings | `all-MiniLM-L6-v2` (sentence-transformers) | `EMBEDDING_MODEL` in `.env` |
| Vector DB | Qdrant (local file) | `./qdrant_data/` |
