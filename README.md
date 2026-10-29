# EasyRift

EasyRift is a web application for browsing and analyzing **League of Legends** data, including player profiles, match history, champion statistics, recommended builds and professional player profiles.

The application is currently available for **local execution only**. There is no active public deployment.

## Technologies

### Backend

- **Java 21 and Spring Boot 4.0.2** — REST API and application logic.
- **Spring Web MVC** — HTTP request handling.
- **Spring Data JPA and Hibernate** — database access and object-relational mapping.
- **PostgreSQL 16** — persistent storage.
- **Spring RestClient** — integration with the Riot Games API.
- **Maven** — dependency management and builds.

### Frontend

- **React 19 and TypeScript** — component-based user interface.
- **React Router** — client-side navigation.
- **Tailwind CSS 4** — styling.
- **Vite 7** — development server and builds.
- **Fetch API** — communication with the backend.

### Infrastructure

- **Docker and Docker Compose** — backend and database containers.
- **Riot Games API** — player, ranked, match and live-game data.

## Architecture

EasyRift uses a **client–server architecture**, with a React frontend consuming a Spring Boot REST API over HTTP/JSON.

The backend follows a **layered architecture**:

```text
React Frontend
      │ HTTP / JSON
      ▼
Controllers
      │
      ▼
Services
      ├── Repositories ──► PostgreSQL
      └── Riot API Clients ──► Riot Games API
```

- **Controllers** expose REST endpoints.
- **Services** implement application logic, data synchronization and statistics calculations.
- **Repositories and models** manage persistence through Spring Data JPA.
- **DTOs** define request and response data.
- **Riot API clients** encapsulate external API calls.
- **Scheduled tasks** update professional player data and champion statistics.
- **Centralized exception handling** produces structured API error responses.

The frontend is organized into pages, reusable components, API functions, shared utilities and TypeScript types.

## Features

- Search for players by Riot ID and platform.
- View ranked information, player statistics and match history.
- Refresh player data and check current-game information.
- Inspect match scoreboards, participant statistics and item builds.
- Browse champions and explore statistics and builds by role.
- View recommended builds and builds used by professional players.
- Browse professional player profiles and their recent builds.

## Project Structure

```text
.
├── backend/    # Spring Boot REST API, persistence and Riot API integration
├── frontend/   # React and TypeScript application
└── deploy/     # Docker Compose and environment configuration
```

## Running Locally

### Requirements

- [Docker Desktop](https://www.docker.com/products/docker-desktop/), including Docker Compose.
- [Node.js](https://nodejs.org/en/download) 22.12 or later, including npm.
- A valid API key from the [Riot Developer Portal](https://developer.riotgames.com/).

Java, Maven and PostgreSQL are provided by the Docker setup and do not need to be installed separately.

### 1. Clone the Repository

```bash
git clone https://github.com/fmonteroi/easyrift.git
cd easyrift
```

### 2. Configure the Environment

Copy `deploy/.env.example` to `deploy/.env`.

Set `RIOT_API_KEY` to your Riot API key and update the database credentials as needed. Keep the PostgreSQL and Spring datasource values consistent.

The example configuration uses:

- Backend: `http://localhost:8080`
- Frontend: `http://localhost:5173`
- Database hostname inside Docker: `db`

Development API keys expire and must be renewed when necessary.

### 3. Start the Backend and Database

From `deploy`:

```bash
docker compose up -d --build
```

To inspect backend startup logs:

```bash
docker compose logs -f backend
```

### 4. Start the Frontend

In a separate terminal, from `frontend`:

```bash
npm install
npm run dev
```

Open the URL printed by Vite, normally **http://localhost:5173**.

The frontend connects to `http://localhost:8080` by default. To change it, create `frontend/.env.local`:

```dotenv
VITE_API_URL=http://localhost:8080
```

Restart Vite after changing this variable. If you change the frontend origin, also update `APP_CORS_ALLOWED_ORIGINS` in `deploy/.env`.

### Subsequent Runs

From `deploy`:

```bash
docker compose up -d
```

From `frontend`:

```bash
npm run dev
```

Rebuild the backend image after backend code changes using `docker compose up -d --build`.

### Stop the Application

Stop Vite with `Ctrl+C`, then run from `deploy`:

```bash
docker compose down
```

Database data is preserved in a Docker volume. To remove it as well:

```bash
docker compose down -v
```

## Frontend Checks

Run from `frontend`:

```bash
npm run lint
npm run build
```

These commands run ESLint, check TypeScript types and generate the production build.

## Data Availability

Features that retrieve Riot data require an internet connection and a valid API key, and are subject to Riot API restrictions and rate limits.

Some statistics and recommended builds depend on the match data collected by the local application and may initially be unavailable.
