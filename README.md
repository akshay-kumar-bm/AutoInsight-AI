# AutoInsight AI

Multi-agent data analytics platform: ask questions about a database or an uploaded dataset in natural language and get SQL results, Python analysis and charts, produced by collaborating AutoGen agents.

## What it does
- **Database analytics:** a Database agent turns English into SQL (LangChain SQL toolkit over SQLite, sample `database/ecommerce.db`).
- **Visualization:** a Visualization agent calls plotting tools (bar, line, scatter, pie, histogram) and saves PNGs.
- **Data analysis:** upload CSV/Excel/JSON; a Data-Analysis agent writes Python that a Code-Executor agent runs inside Docker; a Human agent supports human-in-the-loop.
- Teams are coordinated with AutoGen `RoundRobinGroupChat`/`SelectorGroupChat` (`teams/team_manager.py`), with streaming responses.

## Interfaces
FastAPI app (`app_fastapi.py`) with HTML pages `/`, `/dataanalyst`, `/database`, `/visualization`, `/docs`, plus Streamlit apps in `streamlit/`.
Endpoints: `GET /health`, `POST /api/v1/database/query`, `POST /api/v1/visualization/create`, `POST /api/v1/data-analysis/upload`, `POST /api/v1/data-analysis/query`, `GET /api/v1/files`, `GET /api/v1/files/{filename}`, `POST /api/v1/teams/reset`.

## Tech stack
Python, AutoGen (agentchat/core/ext 0.6.1), LangChain, OpenAI (gpt-4o-mini via autogen client, gpt-4.1-mini in LangChain helper), FastAPI + Uvicorn, Streamlit, Matplotlib/Plotly/Seaborn, SQLite/SQLAlchemy, Docker.

## Structure
```
app_fastapi.py   agent/   teams/   tool/   util/   config/   database/
streamlit/       templates/   notebook/ (experiments)   coding/ (sample data)
Dockerfile       requirements.txt   .env.example
```

## Setup
```bash
python -m venv venv && source venv/bin/activate
pip install -r requirements.txt
cp .env.example .env     # set OPENAI_API_KEY (also WORKERS, DEBUG, PORT)
python app_fastapi.py    # http://localhost:8080 (PORT env overrides)
```
Docker: `docker build -t autoinsight-ai .` then `docker run -p 5001:5001 --env-file .env autoinsight-ai` (Dockerfile serves uvicorn on 5001 with a /health check). Data-analysis code execution needs Docker available on the host.
The original README links to a Cloud Run live demo and demo videos; these could not be verified.

## Limitations
- Repo contains committed `__pycache__`, a server log, trained `.pkl` models, sample datasets, and many generated plots.
- Typos in module names (`dataanalsys`, `code_excuter`); some duplicate/"copy" files.
- Original README claims "enterprise-grade" and tests/LICENSE that are not present in the repo.
