# Detour

Created for **HackYeah 2026**.

Detour is a walking trip planner for Kraków that calculates the greenest available route between selected attractions. It analyzes the OpenStreetMap pedestrian network and green-space scores to favor routes through parks and greener urban areas.

Users can discover attractions, filter them by category and accessibility, build an itinerary, and calculate a personalized walking route.

## Features

- Kraków attraction database populated from OpenStreetMap through the Overpass API
- Attraction search and filtering by category and accessibility
- Ranking by community likes
- Anonymous voting without user accounts
- Itinerary builder with manual stop ordering
- Trip duration limit tracking
- Multi-stop pedestrian routing
- Preference for green and well-lit route segments
- Interactive Leaflet map with markers, popups, and GeoJSON layers

## Technology stack

### Frontend

- React 18 and TypeScript
- Vite
- Tailwind CSS
- Zustand
- Leaflet and React Leaflet
- Lucide React

### Backend

- Python and FastAPI
- SQLAlchemy
- SQLite
- OSMnx and NetworkX
- OpenStreetMap and Overpass API

## Running with Docker

Docker and Docker Compose are required.

```bash
cp backend/.env.example backend/.env
cp frontend/.env.example frontend/.env
docker compose up --build
```

The services will be available at:

- Frontend: http://localhost:5173
- Backend: http://localhost:8000
- Swagger UI: http://localhost:8000/docs
- ReDoc: http://localhost:8000/redoc

The SQLite database is stored in `backend/data/app.db`. Stopping the containers does not remove the data.

## Running locally

### Backend

Python 3.12 and [uv](https://docs.astral.sh/uv/) are required.

```bash
cd backend
cp .env.example .env
```

When running outside Docker, set a relative database path in `backend/.env`:

```env
DATABASE_URL=sqlite:///./data/app.db
```

Install the dependencies and start the API:

```bash
uv sync
uv run uvicorn app.main:app --reload
```

The backend will be available at http://localhost:8000.

### Frontend

Node.js 20 or newer is required.

```bash
cd frontend
cp .env.example .env
npm install
npm run dev
```

The frontend will be available at http://localhost:5173.

## OpenStreetMap data

To download or refresh attractions from the Overpass API:

```bash
cd backend
uv run python -m app.scripts.seed_attractions --force
```

Automatic attraction import during application startup can be enabled in `backend/.env`:

```env
SEED_ATTRACTIONS_ON_STARTUP=true
```

The initial import may take several minutes and depends on the availability of public Overpass servers.

The pedestrian graph with green-space scores is loaded from:

```text
backend/data/krakow_green.graphml
```

To regenerate it:

```bash
cd backend/scripts
uv run python green_score_krakow.py
```

## Main API endpoints

- `GET /api/health` — backend health status
- `GET /api/attractions` — list, search, and filter attractions
- `GET /api/attractions/random` — random attractions
- `GET /api/attractions/names` — attraction names
- `PUT /api/attractions/{id}/like` — add a like
- `DELETE /api/attractions/{id}/like` — remove a like
- `POST /api/route/optimize` — calculate a multi-stop route
- `POST /api/routing/` — calculate a route between two points

The frontend automatically creates an anonymous browser identifier and sends it in the `X-Visitor-Id` header. This allows each browser to cast only one vote per attraction.

## Project structure

```text
.
├── backend/
│   ├── app/
│   │   ├── api/          # FastAPI endpoints
│   │   ├── models/       # SQLAlchemy models
│   │   ├── schemas/      # Pydantic schemas
│   │   ├── scripts/      # data import and demo data
│   │   └── services/     # Overpass integration
│   ├── data/             # SQLite database and routing graph
│   └── scripts/          # green routing graph generation
├── frontend/
│   └── src/
│       ├── api/          # API client
│       ├── components/   # interface and map
│       ├── store/        # Zustand state
│       └── types/        # TypeScript types
└── docker-compose.yml
```

## Verification

Frontend:

```bash
cd frontend
npm run build
```

Backend:

```bash
cd backend
uv run python -m compileall app
```

## Authors

- Filip Sobieraj
- Szymon Wołoszyn
- Jakub Wieliczko
- Radosław Firlej
- Alicja Borek
