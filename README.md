# HoloSummary

HoloSummary is a full-stack app that tracks Hololive streams, fetches transcripts, and generates long-form summaries using OpenRouter. It includes a Spring Boot API, a FastAPI transcript crawler, and a React web UI.

## Repos and services

- HoloSummaryBackend
  - Spring Boot (Java 21)
  - PostgreSQL database
  - Python transcript crawler (FastAPI)
- HoloSummaryFrontend
  - React 19 + Vite + TypeScript

## Prerequisites

- Docker and Docker Compose (recommended for backend + crawler + DB)
- Node.js and npm (for frontend)
- Java 21 (if running backend without Docker)
- Python 3.13 recommended (if running transcript crawler without Docker)

## Quick start (Docker Compose)

1) Create an env file for the backend services:

```bash
cd HoloSummaryBackend
cat > .env <<'EOF'
DB_USERNAME=holosummary
DB_PASSWORD=holosummary
OPEN_ROUTER_API_KEY=replace-me
GOOGLE_CLIENT_ID=replace-me
GOOGLE_CLIENT_SECRET=replace-me
JWT_SECRET_KEY=replace-me
FRONTEND_URL=http://localhost:5173
WINDOWS_TAILSCALE_IP=127.0.0.1
PROXY_PORT=8899
EOF
```

2) Start backend services:

```bash
docker compose up --build
```

3) Start the frontend:

```bash
cd ../HoloSummaryFrontend
npm install
npm run dev
```

The frontend dev server proxies /api and /oauth2 to http://localhost:8080.

## Running without Docker

### Backend (Spring Boot)

From HoloSummaryBackend, set these variables and run:

```bash
export DATABASE_URL=jdbc:postgresql://localhost:5432/holo_summary
export DATABASE_USERNAME=holosummary
export DATABASE_PASSWORD=holosummary
export TRANSCRIPTION_API_URL=http://localhost:8000/api/transcript
export OPEN_ROUTER_API_KEY=replace-me
export GOOGLE_CLIENT_ID=replace-me
export GOOGLE_CLIENT_SECRET=replace-me
export JWT_SECRET_KEY=replace-me
export FRONTEND_URL=http://localhost:5173
./mvnw spring-boot:run
```

### Transcript crawler (FastAPI)

From HoloSummaryBackend/python-transcript-crawler:

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
export WINDOWS_TAILSCALE_IP=127.0.0.1
export PROXY_PORT=8899
uvicorn crawler:app --host 0.0.0.0 --port 8000
```

Note: the crawler is designed to use a residential proxy (for example via Tailscale). Adjust the env vars to match your proxy.

### Frontend (React)

From HoloSummaryFrontend:

```bash
npm install
npm run dev
```

## Configuration reference

### Docker Compose (backend)

Required variables for HoloSummaryBackend/.env:

- DB_USERNAME
- DB_PASSWORD
- OPEN_ROUTER_API_KEY
- GOOGLE_CLIENT_ID
- GOOGLE_CLIENT_SECRET
- JWT_SECRET_KEY
- FRONTEND_URL
- WINDOWS_TAILSCALE_IP (for the transcript crawler proxy)
- PROXY_PORT (for the transcript crawler proxy)

### Backend (non-Docker)

- DATABASE_URL
- DATABASE_USERNAME
- DATABASE_PASSWORD
- TRANSCRIPTION_API_URL
- OPEN_ROUTER_API_KEY
- GOOGLE_CLIENT_ID
- GOOGLE_CLIENT_SECRET
- JWT_SECRET_KEY
- FRONTEND_URL

## Ports

- Backend API: 8080
- Transcript crawler: 8000
- PostgreSQL: 5432
- Frontend dev server: 5173

## Production (Docker)

A production compose file is included for image-based deployment:

```bash
cd HoloSummaryBackend
docker compose -f prod.docker-compose.yml up -d
```

The prod compose uses images from GHCR and only exposes the API on 127.0.0.1:8080 (intended to sit behind a reverse proxy).

## Notes

- Springdoc/Swagger is disabled by default.
- The frontend expects the API to be available under /api.
