# Compose API + DB + ETL

## Objetivo

Levantar stack completo con `docker compose` y ejecutar ETL bajo demanda.

## Crear archivo

Ruta: `labs/02-docker-analitica/trabajo/stack-analitica/docker-compose.yml`

## Demo breve

```bash
cd labs/02-docker-analitica/trabajo/stack-analitica
docker compose up --build
# otra terminal
curl -sS http://localhost:8000/health
docker compose --profile etl run --rm etl
```

# LABORATORIO

- [step-01-estructura-stack.md](laboratorio/step-01-estructura-stack.md)
