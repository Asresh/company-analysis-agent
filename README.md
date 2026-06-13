# Company Analysis Agent

A multi-agent system that turns a single company name or ticker into a full investment analysis. You give it a company; a team of agents pulls live web data, runs the research, and returns a structured thesis with a buy/hold/sell signal.

Built for the **BuilderShip** hackathon (Composio · Nebius · Tavily).

---

## What it does

- **Input:** a company name or ticker.
- **Process:** specialized agents fan out to gather market data, news, fundamentals, and sentiment, then a synthesis agent reconciles their findings.
- **Output:** an investment thesis, key risks, supporting evidence, and a directional signal.

> _Add a screenshot or demo GIF here — it's the single biggest thing that makes the repo land with judges._
>
> `![Demo](docs/demo.gif)`

---

## Architecture

```
                ┌──────────────────────────┐
                │      Frontend (Vite)     │
                │   React + TypeScript UI  │
                └────────────┬─────────────┘
                             │  HTTP
                ┌────────────▼─────────────┐
                │   Backend (FastAPI)      │
                │   app.main:app           │
                └────────────┬─────────────┘
                             │  orchestrates
            ┌────────────────┼────────────────┐
            ▼                ▼                ▼
   ┌──────────────┐ ┌──────────────┐ ┌──────────────┐
   │ Research     │ │ Market /     │ │ Synthesis /  │
   │ Agent        │ │ Data Agent   │ │ Thesis Agent │
   └──────┬───────┘ └──────┬───────┘ └──────┬───────┘
          │                │                │
          ▼                ▼                ▼
   ┌─────────────────────────────────────────────────┐
   │   Tool / Data layer (MCP: web data + search)     │
   └─────────────────────────────────────────────────┘
```

- **Frontend:** Vite + React + TypeScript.
- **Backend:** FastAPI, entry point `app.main:app`, health check at `GET /health`, interactive docs at `/docs`.
- **Agent layer:** multi-agent orchestration that splits research, data gathering, and synthesis.
- **Data layer:** MCP-based web data and search tooling.

---

## Tech stack

| Layer        | Tech                                      |
|--------------|-------------------------------------------|
| Frontend     | Vite, React, TypeScript, CSS              |
| Backend      | Python 3.11, FastAPI, Uvicorn             |
| Agents       | Multi-agent orchestration                 |
| Data / tools | MCP (web data + search)                   |
| Deploy       | Zeabur / Docker                           |

---

## Quick start

### Prerequisites
- Python 3.11+
- Node 18+
- API keys for your model + data providers (see `.env.example`)

### 1. Clone and configure
```bash
git clone https://github.com/Asresh/company-analysis-agent.git
cd company-analysis-agent
cp .env.example .env   # fill in your keys
```

### 2. Run the backend
```bash
cd backend
pip install -r requirements.txt
uvicorn app.main:app --host 0.0.0.0 --port 8000
```
Verify it's up: open `http://localhost:8000/health` → should return `{"status":"ok"}`.
API docs: `http://localhost:8000/docs`.

### 3. Run the frontend
```bash
cd front_end
npm install
npm run dev
```
Open the printed local URL (usually `http://localhost:5173`).

---

## Environment variables

Copy `.env.example` to `.env` and fill in your own values. **Never commit real tokens.**

```
# example — see .env.example for the full list
BRIGHTDATA_API_TOKEN=
TOKENROUTER_BASE_URL=
TOKENROUTER_API_KEY=
```

---

## Deployment

The deployable backend lives in `backend/`. To deploy on Zeabur:

1. Push this repo to GitHub.
2. Go to `https://zeabur.com/new`, choose GitHub, select this repo.
3. Create a service, set the root directory to `backend`, Python 3.11.
4. **Build:** `pip install -r requirements.txt`
5. **Start:** `uvicorn app.main:app --host 0.0.0.0 --port $PORT`
6. Confirm `https://<your-domain>/health` returns `{"status":"ok"}`.

`backend/zbpack.json` and `backend/Dockerfile` are included for the same production behavior.

---

## Roadmap

- [ ] Add demo GIF / screenshots
- [ ] Route inference through Nebius
- [ ] Add Tavily as the search provider
- [ ] Wire Composio tools into the agent layer
- [ ] Expand the synthesis agent's risk modeling

---

## License

MIT — see `LICENSE` if present, otherwise add one before publishing.
