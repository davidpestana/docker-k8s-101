# Docker Compose con PostgreSQL y Python

## Objetivo

Levantar dos servicios dependientes con una sola definición.

## Crear `docker-compose.yml`

Ruta: `labs/01-docker-basico/trabajo/pg-demo/docker-compose.yml`

```yaml
services:
  db:
    image: postgres:16
    environment:
      POSTGRES_USER: demo
      POSTGRES_PASSWORD: demo
      POSTGRES_DB: demo
  client:
    image: python:3.11-slim
    depends_on:
      - db
    command: >-
      bash -lc "pip install -q psycopg[binary]==3.1.18 &&
      python -u -c \"import psycopg; c=psycopg.connect('host=db user=demo password=demo dbname=demo'); print(c.execute('SELECT 1').fetchone())\""
```

## Demo breve

```bash
cd labs/01-docker-basico/trabajo/pg-demo
docker compose up --abort-on-container-exit
```

## Cierre del bloque

Has cubierto imagen, contenedor, volumen, red y multi-servicio.

# LABORATORIO

- [step-01-estructura-proyecto.md](laboratorio/step-01-estructura-proyecto.md)
