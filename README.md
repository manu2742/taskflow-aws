# TaskFlow: Full-Stack Task Manager with AWS Deployment

![CI](https://github.com/manu2742/taskflow-aws/actions/workflows/ci.yml/badge.svg)

A complete full-stack project covering **REST API design, authentication, database design, containerization, CI/CD and AWS cloud deployment**.

| Layer | Technology |
|---|---|
| Frontend | React 18 + Vite, served by nginx |
| Backend | Node.js 20, Express, Zod validation |
| Auth | JWT (Bearer tokens), bcrypt password hashing |
| Database | PostgreSQL 16 (Docker locally, AWS RDS in production) |
| Security | Helmet, CORS, rate limiting, per-user data isolation |
| DevOps | Docker, Docker Compose, GitHub Actions (CI + deploy) |
| Cloud | AWS EC2, RDS, IAM, security groups, CloudWatch Logs |
| Testing | Jest + Supertest (API integration tests) |

## Features
- Register / login with JWT authentication
- Create, read, update, delete tasks; filter by status; pagination
- Input validation and consistent JSON error responses
- `/api/health` endpoint with database check (used by deploy smoke test)
- Users can only access their own tasks

## Architecture
```
Browser -> nginx (React build + /api reverse proxy) -> Express API -> PostgreSQL (RDS)
                                      EC2 (Docker)                      |
                                                CloudWatch Logs <-------+ (container logs)
```

## Run locally (Docker)
```bash
cp .env.example .env        # set JWT_SECRET
docker compose up --build
# App:  http://localhost
# API:  http://localhost/api/health
```

## Run without Docker
```bash
# 1) PostgreSQL running locally, then:
cd backend && cp ../.env.example .env && npm install && npm run dev      # API on :4000
cd frontend && npm install && npm run dev                                 # UI on :5173
```

## Tests
```bash
cd backend && npm install && npm test     # needs DATABASE_URL pointing to a Postgres database
```

## Project structure
```
taskflow-aws/
|-- backend/
|   |-- src/ (app.js, server.js, config.js, db.js, routes/, middleware/)
|   |-- db/schema.sql
|   |-- tests/api.test.js
|   `-- Dockerfile
|-- frontend/
|   |-- src/ (App.jsx, api.js, main.jsx, styles.css)
|   |-- nginx.conf
|   `-- Dockerfile
|-- docs/ (API.md, AWS_DEPLOYMENT.md)
|-- scripts/deploy.sh
|-- .github/workflows/ (ci.yml, deploy.yml)
|-- docker-compose.yml          # local: app + postgres
|-- docker-compose.prod.yml     # AWS: app + RDS + CloudWatch logging
`-- .env.example
```

## Deploy to AWS
See [docs/AWS_DEPLOYMENT.md](docs/AWS_DEPLOYMENT.md) (EC2 + RDS + IAM + CloudWatch + GitHub Actions) and [docs/API.md](docs/API.md) for the endpoint reference.

## Live demo
`http://<your-ec2-public-ip>`  <!-- replace after you deploy -->

## What I learned / design decisions
- Parameterized SQL queries everywhere (no string-built user input) to prevent SQL injection.
- JWT in the `Authorization` header; passwords hashed with bcrypt; auth endpoints rate-limited.
- Database schema is idempotent and applied on startup, with retries so the API survives a slow DB start.
- Same Docker images run locally and on EC2; only the compose file (local DB vs RDS) changes.

## License
MIT

## Author
Manoj M - [GitHub](https://github.com/manu2742) | [LinkedIn](https://linkedin.com/in/manoj-m-manu-m) | manojmmanum65@gmail.com
