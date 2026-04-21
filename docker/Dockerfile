# taiga-back/docker/Dockerfile — ARM64-compatible patch
#
# Changes from upstream:
#   1. Switch base image to python:3.11-slim-bookworm (multi-arch, has arm64)
#   2. Install build deps for native extensions on ARM
#   3. Use psycopg2-binary instead of psycopg2 (no libpq-dev compile needed)
#
# Apply this by replacing taiga-back/docker/Dockerfile in your fork
# with the content below.
#
# The requirements.txt change (psycopg2 -> psycopg2-binary) must also be made.

FROM python:3.11-slim-bookworm

# Build deps — needed on ARM for some pip packages
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    libffi-dev \
    libssl-dev \
    libpq-dev \
    git \
    gettext \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /taiga-back

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

RUN python manage.py compilemessages || true

EXPOSE 8000

ENTRYPOINT ["/taiga-back/docker/entrypoint.sh"]
