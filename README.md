# Django Book Shop - Docker & CI/CD Deployment Pipeline

A containerized Django Book Shop application built using Django, PostgreSQL, Nginx, Docker, GitHub Actions, Docker Hub, and AWS EC2.

This project implements both:

- Phase 1 → Dockerized Django Deployment
- Phase 2 → Complete CI/CD Pipelines

---

# Technologies Used

- Django
- PostgreSQL 15
- Nginx
- Docker
- Docker Compose
- GitHub Actions
- Docker Hub
- AWS EC2
- Gunicorn

---

# Repository Structure

```text
.github/workflows/
│
├── dev.yml
├── test.yml
└── prod.yml

book-shop/
│
├── artifacts/
├── Dockerfile
├── docker-compose.yml
└── ...
```

---

# Phase 1 - Dockerized Django Application

## Services

- **db** → PostgreSQL 15 database
- **backend** → Django application with Gunicorn
- **nginx** → Nginx reverse proxy

---

# Requirements

- Docker
- Docker Compose

---

# Clone Repository

```bash
git clone https://github.com/huzaifa018-1/book-shop-docker.git
cd book-shop-docker/book-shop
```

---

# Local Setup & Installation

## 1. Create Environment File

```bash
cp .env.example .env
```

---

## 2. Build and Run Containers

```bash
docker compose up --build
```

---

## 3. Run Database Migrations

```bash
docker compose exec backend python manage.py migrate
```

---

## 4. Create Superuser

```bash
docker compose exec backend python manage.py createsuperuser
```

---

# Access the Application

| Service | URL |
|---|---|
| Main Application | http://localhost:8000 |
| Django Admin | http://localhost:8000/admin |
| Nginx | http://localhost |

---

# Environment Variables

Example `.env` configuration:

```env
POSTGRES_DB=book_shop_db
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
SECRET_KEY=django-secret-key
ALLOWED_HOSTS=*
```

---

# Common Docker Commands

## Start Containers

```bash
docker compose up --build
```

## Stop Containers

```bash
docker compose down
```

## Remove Volumes and Data

```bash
docker compose down -v
```

## View Logs

```bash
docker compose logs backend
```

## Run Django Management Commands

```bash
docker compose exec backend python manage.py <command>
```

---

# Phase 2 - CI/CD Pipelines

This phase implements a complete CI/CD workflow using GitHub Actions, Docker Hub, and AWS EC2.

The workflow is divided into three independent environments:

- Development Environment (dev)
- Testing Environment (test)
- Production Environment (prod)

---

# Branch Strategy

| Branch | Deployment Philosophy | Description |
|---|---|---|
| dev | Artifact-First | Build and deploy using committed artifacts |
| test | Image-First | Rebuild image and push to Docker Hub |
| prod | Promotion-Only | Pull tested image only (no build) |

---

# Dev Pipeline - Artifact First

Triggered automatically on every push to the `dev` branch.

## Pipeline Steps

1. Build application artifact
2. Save artifact inside:

```text
book-shop/artifacts/
```

3. Commit artifact back to repository
4. Build Docker image FROM the artifact
5. Deploy containers to AWS EC2

## Why Artifact-First?

The artifact becomes the single source of truth.

This guarantees that the deployed container contains exactly what was packaged during the build stage.

---

# Test Pipeline - Image First

Triggered automatically on every push to the `test` branch.

## Pipeline Steps

1. Rebuild application artifact from source
2. Build fresh Docker image
3. Push image to Docker Hub
4. Pull and deploy image on EC2

## Docker Hub Repository

```text
huzaifa018/book-shop
```

## Example Image Tags

```text
test-1
test-2
test-3
test-7
```

## Why Image-First?

This verifies build reproducibility.

The pipeline proves that the application can always be rebuilt from source code without depending on previously generated artifacts.

---

# Prod Pipeline - Promotion Only

Triggered automatically on every push to the `prod` branch.

## Pipeline Steps

1. Read deployment version from GitHub Actions variable:

```text
IMAGE_VERSION
```

2. Pull tested image from Docker Hub
3. Deploy image to EC2

## Important Rule

The production pipeline NEVER builds Docker images.

Only previously tested images are promoted to production.

---

# GitHub Actions Workflows

Located inside:

```text
.github/workflows/
```

Files:

```text
dev.yml
test.yml
prod.yml
```

---

# GitHub Actions Variables

Repository Variables:

```text
IMAGE_VERSION
EC2_HOST
REGISTRY_NAME
```

---

# GitHub Actions Secrets

Repository Secrets:

```text
DOCKERHUB_USERNAME
DOCKERHUB_TOKEN
EC2_SSH_KEY
```

Sensitive credentials are never committed to the repository.

---

# AWS EC2 Deployment

All environments deploy to the same EC2 instance.

To avoid conflicts between environments, the project uses:

- Different ports
- Different compose project names
- Separate `.env` files
- Independent containers

---

# Deployment Ports

| Environment | Backend Port | Nginx Port |
|---|---|---|
| dev | 8001 | 8081 |
| test | 8002 | 8082 |
| prod | 8003 | 8083 |

---

# Docker Compose Project Names

| Environment | Compose Project |
|---|---|
| dev | dev |
| test | test |
| prod | prod |

---

# Docker Images Used

| Service | Image |
|---|---|
| Database | postgres:15 |
| Reverse Proxy | nginx:alpine |
| Application | huzaifa018/book-shop |

---

# Docker Compose Deployment Strategy

The deployment uses image-based services instead of local Docker builds.

Example:

```yaml
backend:
  image: ${DOCKER_IMAGE}
```

This allows environments to pull prebuilt images directly from Docker Hub.

---

# Artifact Storage

Artifacts generated by the `dev` pipeline are stored inside:

```text
book-shop/artifacts/
```

Each artifact is uniquely versioned and preserved as part of the deployment history.

---

# Security Practices

The project follows secure CI/CD practices:

- Secrets stored in GitHub Secrets
- No credentials committed to repository
- SSH deployment using private key authentication
- Production deployment controlled by IMAGE_VERSION variable

---

# CI/CD Benefits

This pipeline architecture provides:

- Automated deployments
- Reproducible builds
- Version-controlled releases
- Environment separation
- Faster testing and deployment
- Reduced manual deployment errors

---

# Screenshots Included

- Dev pipeline success
- Test pipeline success
- Prod pipeline success
- Docker Hub repository and tags
- Running containers on EC2
- Deployment environments

---

# Author

Huthaifa Nafith

Special Topics in Software Engineering  
Phase 1 & Phase 2 — Docker & CI/CD Pipelines
