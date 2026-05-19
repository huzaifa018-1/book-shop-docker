FROM python:3.11-slim

WORKDIR /app

RUN apt-get update && apt-get install -y \
    postgresql-client \
    && rm -rf /var/lib/apt/lists/*

ARG ARTIFACT_FILE=""

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

RUN if [ -n "$ARTIFACT_FILE" ] && [ -f "artifacts/$ARTIFACT_FILE" ]; then \
        rm -rf books book_shop templates manage.py && \
        tar -xzf artifacts/$ARTIFACT_FILE; \
    fi

RUN useradd -m -u 1000 django && chown -R django:django /app

USER django

EXPOSE 8000

CMD ["gunicorn", "--bind", "0.0.0.0:8000", "--workers", "4", "book_shop.wsgi:application"]
