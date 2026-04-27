# ETL batch con Docker

## Objetivo

Crear proceso batch separado del servicio API.

## Crear archivos

- `labs/02-docker-analitica/trabajo/stack-analitica/etl/requirements.txt`
- `labs/02-docker-analitica/trabajo/stack-analitica/etl/etl.py`
- `labs/02-docker-analitica/trabajo/stack-analitica/etl/Dockerfile`

## Demo breve

```bash
cd labs/02-docker-analitica/trabajo/stack-analitica
docker build -t analytics-etl:0.1.0 ./etl
```

## Continuar

- [compose-api-db-etl.md](compose-api-db-etl.md)
