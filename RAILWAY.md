# Railway deployment

This repository contains two deployable services:

- `backend/` — FastAPI + real FEM engine
- `frontend/` — React/Vite UI

## Backend service
Root directory: `backend`
Start command:
`uvicorn app.main:app --host 0.0.0.0 --port $PORT`

## Frontend service
Root directory: `frontend`
Build command:
`npm install && npm run build`
Start command:
`npm run preview -- --host 0.0.0.0 --port $PORT`

Set the frontend variable at build time:
`VITE_API_URL=https://<BACKEND-RAILWAY-DOMAIN>/api`

Set the backend variable:
`CORS_ORIGINS=https://<FRONTEND-RAILWAY-DOMAIN>`

For local development, the existing localhost defaults remain valid.
