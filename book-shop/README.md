# Django Book Shop - Docker Deployment

A containerized Django Book Shop application using Docker Compose with PostgreSQL.

## Requirements

- Docker
- Docker Compose

## Setup & Installation

1. Clone the repository:
```bash
git clone https://github.com/huzaifa018-1/book-shop-docker.git
cd book-shop-docker
```

2. Create environment file from template:
```bash
cp .env.example .env
```

3. Build and run containers:
```bash
docker-compose up --build
```

4. Run migrations (in a new terminal):
```bash
docker-compose exec backend python manage.py migrate
```

5. Create superuser for admin:
```bash
docker-compose exec backend python manage.py createsuperuser
```

6. Access the application:
- Main app: http://localhost:8000
- Admin panel: http://localhost:8000/admin

## Services

- **db**: PostgreSQL 15 database
- **backend**: Django application with Gunicorn

## Environment Variables

See `.env.example` for required environment variables:
- POSTGRES_DB: Database name
- POSTGRES_USER: Database user
- POSTGRES_PASSWORD: Database password
- SECRET_KEY: Django secret key
- ALLOWED_HOSTS: Allowed hostnames

## Stopping Containers

```bash
docker-compose down
```

## Removing Volumes

```bash
docker-compose down -v
```
