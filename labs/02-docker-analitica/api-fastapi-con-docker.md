# API FastAPI con Docker

## Objetivo

Crear servicio HTTP base para el stack analítico.

## Crear archivos

- `labs/02-docker-analitica/trabajo/stack-analitica/api/requirements.txt`
- `labs/02-docker-analitica/trabajo/stack-analitica/api/app/main.py`
- `labs/02-docker-analitica/trabajo/stack-analitica/api/Dockerfile`

## Demo breve

```bash
cd labs/02-docker-analitica/trabajo/stack-analitica
docker build -t analytics-api:0.1.0 ./api
docker run --rm -p 8000:8000 analytics-api:0.1.0
```

## Continuar

- [etl-batch-con-docker.md](etl-batch-con-docker.md)
## Navegacion del libro

- [Anterior](README.md)
- [Siguiente](etl-batch-con-docker.md)
