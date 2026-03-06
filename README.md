# MIRAGE

<p align="center">
  <b>AI-powered research workspace for documents, insights, grounded chat, and visual knowledge graphs.</b>
</p>

<p align="center">
  <img alt="React" src="https://img.shields.io/badge/Frontend-React%2018-61dafb?style=for-the-badge&logo=react&logoColor=white" />
  <img alt="Node" src="https://img.shields.io/badge/Backend-Node%20%2B%20Express-339933?style=for-the-badge&logo=node.js&logoColor=white" />
  <img alt="Python" src="https://img.shields.io/badge/Worker-FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white" />
  <img alt="MongoDB" src="https://img.shields.io/badge/Database-MongoDB-47A248?style=for-the-badge&logo=mongodb&logoColor=white" />
</p>

---

## Why Mirage

Mirage turns raw PDFs into an interactive intelligence layer:

- **Upload and process** long research documents asynchronously.
- **Generate insights** with mapped highlights on annotated PDFs.
- **Chat with context** using project-scoped retrieval over chunk embeddings.
- **Explore relationships** through a visual knowledge graph.

---
## Demostration


https://github.com/user-attachments/assets/ea7793a7-19f0-496e-984a-31763593bc1c


---

## Monorepo Map

| Service | Path | Purpose |
|---|---|---|
| Backend API | `backend/` | Auth, projects, documents, chat, orchestration |
| Frontend App | `frontend/` | Dashboard, viewer, insights UI, chat, graph |
| Worker Service | `worker/` | PDF extraction, chunking, embeddings, insight generation |
| Orchestration | `docker-compose.yml` | Full local stack (frontend + backend + worker + mongo) |

Service docs:

- [Backend README](backend/README.md)
- [Frontend README](frontend/README.md)
- [Worker README](worker/README.md)

---

## Quick Start (Local)

### 1) Prerequisites

- Node.js `20+`
- Python `3.11+`
- MongoDB (local or Atlas)
- Tesseract OCR
  - macOS: `brew install tesseract`

### 2) Configure environment files

```bash
cp backend/.env.example backend/.env
cp frontend/.env.example frontend/.env
cp worker/.env.example worker/.env
```

Minimum required values:

- `backend/.env`
  - `OPENROUTER_API_KEY`
  - `UPLOADTHING_TOKEN`
  - `UPLOADTHING_SECRET`
  - `JWT_SECRET` (recommended)
- `worker/.env`
  - `OPENROUTER_API_KEY`
  - `UPLOADTHING_TOKEN`
  - `UPLOADTHING_SECRET`

Port alignment:

- Recommended: `backend/.env` uses `PORT=5001`
- Or set `frontend/.env` as `REACT_APP_API_URL=http://localhost:5000/api`

### 3) Install dependencies

```bash
cd backend && npm install
cd ../frontend && npm install
cd ../worker && python -m venv .venv && source .venv/bin/activate && pip install -r requirements.txt
```

### 4) Run services (3 terminals)

Backend

```bash
cd backend
npm run dev
```

Worker

```bash
cd worker
source .venv/bin/activate
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

Frontend

```bash
cd frontend
npm start
```

### 5) Open

- App: http://localhost:3000
- Backend health: http://localhost:5001/health
- Worker health: http://localhost:8000/health

---

## Docker (One Command)

```bash
docker compose up --build
```

Exposed ports:

- Frontend: `3000`
- Backend: `5001` (container `5000`)
- Worker: `8000`
- MongoDB: `27017`

Stop stack:

```bash
docker compose down
```

---

## Architecture Snapshot

```text
Frontend (React)
      │
      ▼
Backend (Express + MongoDB) ──► OpenRouter
      │
      ├──► UploadThing
      ▼
Worker (FastAPI) → OCR → Chunking → Embeddings → Insights → PDF Annotation
```

Core flows:

- **Upload flow:** frontend uploads via backend, backend creates `Document`, worker processes async.
- **Processing flow:** worker extracts text/boxes, chunks, embeds, generates insights, maps highlights, uploads annotated PDF.
- **Chat flow:** backend retrieves relevant chunks and sends grounded prompts to the LLM.

---

## API Overview

Base URL: `http://localhost:5001/api`

- **Auth** — `POST /auth/signup`, `POST /auth/login`, `GET /auth/me`
- **Projects** — `POST /projects`, `GET /projects`, `GET /projects/:id`, `DELETE /projects/:id`
- **Project Summary** — `POST /projects/:id/summary`, `GET /projects/:id/summary`
- **Documents**
  - `POST /projects/:projectId/upload`
  - `GET /projects/:projectId/documents`
  - `GET /documents/:documentId?projectId=...`
  - `GET /projects/:projectId/documents/:documentId/insights`
  - `POST /projects/:projectId/documents/:documentId/insights`
- **Chat** — `POST /projects/:projectId/chat`
- **Upload Proxy** — `POST /uploadthing-upload`

---

## Troubleshooting

- `401 Invalid or expired token`
  - Sign in again and ensure backend `JWT_SECRET` is stable.
- Document stuck in `PROCESSING`
  - Verify worker is running and reachable via backend `WORKER_URL`.
- Upload fails with `500 Failed to upload file`
  - Recheck `UPLOADTHING_TOKEN` and `UPLOADTHING_SECRET` in backend + worker.
- First worker boot is slow
  - Initial SentenceTransformer model download can take time.
