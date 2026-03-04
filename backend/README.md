# Mirage Backend

Express + MongoDB API for projects, documents, insights, chat, auth, and worker orchestration.

## Stack

- Node.js 20
- Express
- Mongoose
- JWT auth (`jsonwebtoken`)
- UploadThing server SDK (`uploadthing`)

## Scripts

```bash
npm run dev    # nodemon server.js
npm start      # node server.js
```

Default local port is `5000`, but this repo’s frontend expects backend on `5001` in local dev (set `PORT=5001` in `.env` for local consistency).

## Environment

Copy template:

```bash
cp .env.example .env
```

Variables:

- `PORT` — API port (set `5001` for local dev with current frontend defaults)
- `MONGO_URI` — Mongo connection string
- `FRONTEND_URL` — CORS allowed origin (ex: `http://localhost:3000`)
- `WORKER_URL` — worker base URL (ex: `http://localhost:8000`)
- `OPENROUTER_API_KEY` — required for chat + summary generation
- `UPLOADTHING_TOKEN` — required for upload endpoint
- `UPLOADTHING_SECRET` — required by UploadThing integration
- `JWT_SECRET` — recommended custom secret for auth token signing
- `HF_TOKEN` — optional legacy value (not required by current backend logic)

## Run locally

```bash
npm install
npm run dev
```

Health:

- `GET http://localhost:5001/health` (or your configured `PORT`)

## API routes

Base path: `/api`

### Auth

- `POST /auth/signup`
- `POST /auth/login`
- `GET /auth/me` (Bearer token required)

### Projects

- `POST /projects`
- `GET /projects`
- `GET /projects/:id`
- `DELETE /projects/:id`
- `POST /projects/:id/summary`
- `GET /projects/:id/summary`
- `GET /projects/:projectId/knowledge-graph`

### Documents and insights

- `POST /projects/:projectId/upload`
- `GET /projects/:projectId/documents`
- `GET /documents/:documentId?projectId=:projectId`
- `GET /projects/:projectId/documents/:documentId/insights`
- `POST /projects/:projectId/documents/:documentId/insights`

### Chat

- `POST /projects/:projectId/chat`

### UploadThing proxy endpoints

- `POST /uploadthing-upload`
- `POST /uploadthing-presign` (legacy response)

## Docker notes

- Container listens on port `5000`.
- In `docker-compose.yml`, host `5001` maps to container `5000`.
- Backend depends on Mongo and worker health checks.
