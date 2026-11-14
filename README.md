# MARSCOPE

Martian Surface Observation, Planning & Exploration System — a React/Vite and Express/MongoDB mission planning workspace.

## Run locally

Requirements: Node.js 20 or newer and npm.

```sh
npm install
Copy-Item server/.env.example server/.env
npm run dev
```

The Vite client runs at `http://localhost:5173`; the API runs at `http://localhost:4000`. MongoDB, authentication, and Gemini are optional for local exploration. Set their environment variables in `server/.env` to enable those services. Set `VITE_API_URL` in a client `.env` only when the API is not at `http://localhost:4000/api`.

## Data provenance and limits

- NASA Mars Trek serves planetary map layers via OGC WMTS. The explorer currently shows an interactive equirectangular *demonstration visualization*, not NASA imagery tiles. The product attribution and official service documentation are linked in the app.
- MOLA elevation is archived by NASA's Planetary Data System and available as global topography in Mars Trek. This project does not include a regional MOLA raster or sample elevation endpoint.
- The A* endpoint is real and deterministic, but its terrain costs come from a synthetic regional grid. Responses label this `DEMONSTRATION GRID · SYNTHETIC, NOT MOLA`. Distance and terrain metrics are algorithm demonstrations and are not operational guidance or a survival probability.
- NASA's legacy Mars Rover Photos API is an archive endpoint. The backend proxies supported rover/sol queries, caches successful results in memory, and reports unavailable or unsupported results clearly. It is not live telemetry.
- Gemini answers require `GEMINI_API_KEY`; the key stays on the server. Responses use only supplied app context and explain when configuration or context is missing.
- MongoDB-backed routes, saved locations and plans require `MONGODB_URI`; registration/login additionally require a strong `JWT_SECRET`. Workspace endpoints require a bearer token.

## Services

| Service | Endpoint | Notes |
| --- | --- | --- |
| Health | `GET /api/health` | Reports API and MongoDB state |
| Missions | `GET /api/mars/missions` | Mission metadata/source links |
| Rover observations | `GET /api/mars/observations?rover=curiosity&sol=1000` | Archived NASA API proxy |
| Terrain layers | `GET /api/terrain/layers` | Source and provenance metadata |
| Generate route | `POST /api/routes/generate` | A* and shortest-cost comparison on synthetic grid |
| Assistant | `POST /api/ai/assistant` | Gemini backend integration |
| Auth/workspace | `/api/auth/*`, `/api/routes`, `/api/locations`, `/api/plans` | MongoDB and JWT required |

Route input example:

```json
{"start":[18.44,77.45],"destination":[18.52,77.61]}
```

## Deployment

- **Render:** use `render.yaml` (or configure `server` as root, `npm install`, `npm start`, health path `/api/health`). Add `MONGODB_URI`, `JWT_SECRET`, `CORS_ORIGIN` (the Vercel origin), `NASA_API_KEY` and optionally `GEMINI_API_KEY`.
- **Vercel:** deploy the repository using `vercel.json`; set `VITE_API_URL` to the deployed API base ending in `/api`. Vite variables are public, so never place credentials there.
- MongoDB Atlas: create a database user and permit the backend network. Use a long random JWT secret. Keep all secrets in deployment environment settings.

## Current scope

The project is a runnable MERN foundation with the core module interfaces, route engine, archive proxy, optional account persistence, and grounded AI endpoint. A production terrain product pipeline still requires acquiring, processing, and hosting a local/regional MOLA raster and registering its real CRS/resolution. The Cesium dependency is present for a future Mars globe; the current 3D mode clearly reports its imagery limitation.
