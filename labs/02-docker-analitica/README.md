# Capitulo 3/6 - Docker para analitica (Modulo 3 del temario)

**Tiempo orientativo**: ~4h. Construyes un mini stack **API (FastAPI) + PostgreSQL** y un **job ETL** en contenedor, usando **variables de entorno** y **Compose** como orquestador local.

## Objetivo

- Separar responsabilidades: `api/`, `etl/`, `docker-compose.yml` en la raiz del proyecto.
- Parametrizar con `.env` (sin secretos reales en el repo).
- Entender **build context** por servicio (`build: ./api`).

## Navegacion rapida

- [Anterior: Capitulo 2 - Docker basico](../01-docker-basico/README.md)
- [Siguiente: Capitulo 4 - Kubernetes basico](../03-k8s-basico/README.md)

---

## Estructura que vas a crear (y por que)

```
labs/02-docker-analitica/trabajo/stack-analitica/
  docker-compose.yml
  .env
  api/
    Dockerfile
    requirements.txt
    app/
      main.py
  etl/
    Dockerfile
    requirements.txt
    etl.py
```

- Raiz con **Compose**: describe el sistema completo (servicios, redes, volumentes).
- `api/`: servicio HTTP stateless; escala horizontal en K8s mas adelante.
- `etl/`: batch sencillo; en K8s se convertira en **Job/CronJob**.

---

## Paso 1 - Carpetas base

```bash
mkdir -p labs/02-docker-analitica/trabajo/stack-analitica/api/app
mkdir -p labs/02-docker-analitica/trabajo/stack-analitica/etl
```

---

## Paso 2 - Variables de entorno `.env`

**Crea** `labs/02-docker-analitica/trabajo/stack-analitica/.env`:

```env
POSTGRES_USER=analytics
POSTGRES_PASSWORD=analytics
POSTGRES_DB=analytics
DATABASE_URL=postgresql+psycopg://analytics:analytics@db:5432/analytics
```

Por que: Compose puede inyectar estas variables en contenedores; **no** subas `.env` a repos publicos con claves reales (aqui son de laboratorio).

Anade a la raiz del repo (si no esta) en `.gitignore`:

```gitignore
labs/**/trabajo/**/.env
```

O manten un `.env.example` commiteado. Para el curso, puedes commitear `.env` de demo con password ficticia **solo** si el repo es de practicas; en produccion usa secretos reales fuera de git.

---

## Paso 3 - API FastAPI

**Crea** `labs/02-docker-analitica/trabajo/stack-analitica/api/requirements.txt`:

```text
fastapi==0.115.6
uvicorn[standard]==0.32.1
sqlalchemy==2.0.36
psycopg[binary]==3.2.3
```

**Crea** `labs/02-docker-analitica/trabajo/stack-analitica/api/app/main.py`:

```python
import os
from fastapi import FastAPI
from sqlalchemy import create_engine, text

app = FastAPI()
DATABASE_URL = os.environ["DATABASE_URL"]


@app.get("/health")
def health():
    return {"status": "ok"}


@app.get("/db/ping")
def db_ping():
    engine = create_engine(DATABASE_URL)
    with engine.connect() as conn:
        one = conn.execute(text("SELECT 1")).scalar_one()
    return {"db": int(one)}
```

**Crea** `labs/02-docker-analitica/trabajo/stack-analitica/api/Dockerfile`:

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY app ./app
ENV PYTHONUNBUFFERED=1
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

Por que `uvicorn` como CMD: patron estandar para servir FastAPI en contenedor.

---

## Paso 4 - ETL batch

**Crea** `labs/02-docker-analitica/trabajo/stack-analitica/etl/requirements.txt`:

```text
pandas==2.2.2
sqlalchemy==2.0.36
psycopg[binary]==3.2.3
```

**Crea** `labs/02-docker-analitica/trabajo/stack-analitica/etl/etl.py`:

```python
import os
from sqlalchemy import create_engine, text
import pandas as pd

DATABASE_URL = os.environ["DATABASE_URL"]


def main() -> None:
    engine = create_engine(DATABASE_URL)
    df = pd.DataFrame({"metric": ["a", "b"], "value": [1, 2]})
    with engine.begin() as conn:
        conn.execute(text("CREATE TABLE IF NOT EXISTS metrics (metric TEXT, value INT)"))
        conn.execute(text("DELETE FROM metrics"))
        for _, row in df.iterrows():
            conn.execute(
                text("INSERT INTO metrics (metric, value) VALUES (:m, :v)"),
                {"m": row["metric"], "v": int(row["value"])},
            )
    print("ETL OK, filas:", len(df))


if __name__ == "__main__":
    main()
```

**Crea** `labs/02-docker-analitica/trabajo/stack-analitica/etl/Dockerfile`:

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY etl.py .
ENV PYTHONUNBUFFERED=1
CMD ["python", "-u", "etl.py"]
```

---

## Paso 5 - `docker-compose.yml` multi-servicio

**Crea** `labs/02-docker-analitica/trabajo/stack-analitica/docker-compose.yml`:

```yaml
services:
  db:
    image: postgres:16
    env_file: .env
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U $$POSTGRES_USER -d $$POSTGRES_DB"]
      interval: 5s
      timeout: 5s
      retries: 10

  api:
    build: ./api
    env_file: .env
    depends_on:
      db:
        condition: service_healthy
    ports:
      - "8000:8000"

  etl:
    build: ./etl
    env_file: .env
    depends_on:
      db:
        condition: service_healthy
    profiles:
      - etl

volumes:
  pgdata:
```

Por que `healthcheck`: la API y el ETL **no deben arrancar** hasta que Postgres acepta conexiones; evita carreras aleatorias ("a veces falla el primer arranque").

Por que `profiles: etl`: el ETL es batch; no quieres que se ejecute en cada `docker compose up` si no toca.

---

## Paso 6 - Ejecutar y validar

Levanta API + BD:

```bash
cd labs/02-docker-analitica/trabajo/stack-analitica
docker compose up --build
```

En otra terminal (o pestaña):

```bash
curl -sS http://localhost:8000/health
curl -sS http://localhost:8000/db/ping
```

Ejecuta ETL bajo demanda:

```bash
docker compose --profile etl run --rm etl
```

**Resultado esperado**: `ETL OK` y `/db/ping` devuelve `{"db":1}`.

**Si falla**:

- Error de URL: comprueba que `DATABASE_URL` usa el host `db` (nombre del servicio en Compose), no `localhost`.
- Puerto ocupado: cambia `8000:8000` a `8001:8000` en `docker-compose.yml`.

---

## Entregables

- [ ] `docker compose up` estable con API y BD.
- [ ] `curl /health` y `/db/ping` OK.
- [ ] ETL ejecutado con profile y datos insertados (puedes ampliar la API con un `GET /metrics` como ejercicio).

---

## Navegacion

- [Anterior: Capitulo 2 - Docker basico](../01-docker-basico/README.md)
- [Siguiente: Capitulo 4 - Kubernetes basico](../03-k8s-basico/README.md)
