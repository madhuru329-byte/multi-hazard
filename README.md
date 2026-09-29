# SIH Multi-Hazard Early Warning Prototype

An integrated React dashboard and FastAPI service for demonstrating flood, landslide, and compound-risk estimates. The frontend is served as a production static site by Nginx; Nginx routes `/api` requests to the backend, so the browser uses one origin.

## Run the complete project

Install Docker Desktop (or Docker Engine with the Compose plugin), then run from this folder:

```sh
docker compose up --build
```

Open [http://localhost:8080](http://localhost:8080). The API health endpoint is [http://localhost:8080/api/health](http://localhost:8080/api/health), and interactive API documentation is at [http://localhost:8080/docs](http://localhost:8080/docs).

To stop the project, press Ctrl+C and run `docker compose down`. To change the host port, set `APP_PORT` before starting (for example, `APP_PORT=80` on macOS/Linux or `$env:APP_PORT=80` in PowerShell). If you expose the site at a different origin, set `CORS_ORIGINS` to a comma-separated list of allowed browser origins.

## Local development

Start the backend in one terminal:

```sh
cd backend
python -m venv .venv
# Windows: .venv\Scripts\activate
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --reload --port 8000
```

Start the frontend in another terminal:

```sh
cd frontend
npm ci
npm run dev
```

Open [http://localhost:5173](http://localhost:5173). The Vite development server proxies `/api` requests to the local backend.

## Deployment

The Compose setup builds and runs both services, waits for the API health check before starting the web service, restarts stopped containers, and publishes only the web entry point. For a public deployment, run Compose on a Docker host behind a reverse proxy or platform ingress that provides HTTPS; set the host port and allowed CORS origins for that environment. Review resource limits and monitoring for your host before opening the service to public traffic.

## API

- `GET /api/health` reports service readiness.
- `POST /api/predict` accepts environmental measurements and returns flood, landslide, and compound estimates, response suggestions, and an explanation.

The API validates measurement ranges. See `/docs` for the interactive schema.

## Prototype limitations

The included classifiers are trained during startup on synthetic demonstration data. Their percentages are not calibrated real-world forecasts and must not be used for emergency decisions. The map locations and zones are illustrative. Before operational use, replace synthetic data with locally validated historical observations, evaluate model performance and calibration, integrate official GIS and alerting sources, and have domain experts review the outputs and response guidance.
