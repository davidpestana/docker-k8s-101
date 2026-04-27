# Step 02 - Levantar servicios con Compose

```bash
cd labs/02-docker-analitica/trabajo/stack-analitica
docker compose up --build
```

Validar:

```bash
curl -sS http://localhost:8000/health
curl -sS http://localhost:8000/db/ping
```

## Siguiente

- [step-03-etl-on-demand.md](step-03-etl-on-demand.md)
