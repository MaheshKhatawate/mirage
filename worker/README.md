# Mirage Worker

FastAPI service for asynchronous PDF processing: extraction, chunking, embeddings, insight generation, highlight mapping, and annotated PDF upload.

## Stack

- Python 3.11
- FastAPI + Uvicorn
- PyMuPDF (`fitz`)
- pytesseract + Pillow
- sentence-transformers (`all-MiniLM-L6-v2`)
- OpenRouter API (via `requests`)

## Environment

Copy template:

```bash
cp .env.example .env
```

Variables:

- `OPENROUTER_API_KEY` — required for insight generation
- `UPLOADTHING_TOKEN` — required by upload flow via backend UploadThing proxy
- `UPLOADTHING_SECRET` — required by UploadThing integration
- `BACKEND_URL` — backend base URL for upload callback (default: `http://backend:5000` in container)
- `INSIGHT_CONCURRENCY` — parallel LLM insight jobs (default: `10`)
- `WORKER_LOG_LEVEL` — logging level (default: `INFO`)
- `HF_TOKEN` — optional legacy value

## Install and run locally

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

Health:

- `GET http://localhost:8000/health`

## API endpoints

- `POST /process`
  - body: `{ documentId, projectId, fileUrl }`
  - returns: `{ pageCount, chunks[], insights[], annotatedFileUrl }`
- `POST /embed`
  - body: `{ text }`
  - returns: `{ embedding }`
- `GET /health`

## Processing pipeline

1. Download source PDF from `fileUrl`.
2. Extract page text and block bounding boxes.
3. Chunk page content.
4. Generate embeddings for each chunk.
5. Select important paragraphs.
6. Generate insights + highlight phrases with LLM (concurrency-limited).
7. Map highlight phrases to PDF coordinates.
8. Annotate PDF.
9. Upload annotated PDF through backend upload endpoint.
10. Return structured processing payload to backend.

## Docker notes

- Base image: `python:3.11-slim`.
- Installs `tesseract-ocr` + runtime system libraries.
- Downloads embedding model during image build for faster container startup.
- Exposes port `8000`.
