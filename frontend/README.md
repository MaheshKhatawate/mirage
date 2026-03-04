# Mirage Frontend

React application for authentication, project dashboards, document viewing, insights, chat, and knowledge graph visualization.

## Stack

- React 18
- React Router 6
- Axios
- Tailwind CSS
- `react-force-graph-3d`
- `pdfjs-dist`

## Scripts

```bash
npm start
npm run build
```

## Environment

Copy template:

```bash
cp .env.example .env
```

Variables:

- `REACT_APP_API_URL` — backend API base URL (default: `http://localhost:5001/api`)
- `REACT_APP_UPLOADTHING_URL` — UploadThing client endpoint if using UploadThing React helpers

Notes:

- Current upload flow in `UploadArea` posts to `/api/uploadthing-upload` through the frontend proxy.
- `package.json` includes `proxy: http://localhost:5001` for local development.

## Run locally

```bash
npm install
npm start
```

App URL:

- `http://localhost:3000`

## Main routes

- `/` and `/landing`
- `/signup`
- `/login`
- `/app`
- `/code`
- `/code/:slug`
- `/projects/:projectId`
- `/projects/:projectId/documents/:documentId`

Routes under the app experience are protected by auth context and token storage in localStorage.

## Docker notes

- Multi-stage build (`node:20-alpine` -> `nginx:alpine`).
- Build args set runtime API URLs:
  - `REACT_APP_API_URL`
  - `REACT_APP_UPLOADTHING_URL`
- Container serves static build on port `80` (mapped to host `3000` in compose).
