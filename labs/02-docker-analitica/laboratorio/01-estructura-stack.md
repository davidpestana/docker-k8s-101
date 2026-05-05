# Step 01 - Estructura del stack

## Objetivo del step

Crear una base de proyecto limpia y consistente para levantar un stack analítico con API, base de datos y ETL.

## Contexto

Antes de ejecutar servicios, necesitas una estructura clara. Esto evita errores de rutas en Dockerfile/Compose y facilita depuración.

## Preparación

Trabaja desde la raíz del repositorio.

```bash
pwd
```

## Fundamento del step

Este step no es solo ejecución de comandos: su objetivo es construir criterio técnico. Cada acción busca evitar errores frecuentes de entorno, de configuración o de integración entre servicios. Antes de avanzar, asegúrate de entender qué problema resuelve cada bloque.

## Ejecución guiada

### 1) Crear carpetas principales

```bash
mkdir -p labs/02-docker-analitica/trabajo/stack-analitica/api/app
mkdir -p labs/02-docker-analitica/trabajo/stack-analitica/etl
```

**Por que este paso es importante**

- Separas responsabilidades desde el inicio: `api` (servicio online) y `etl` (proceso batch).
- Evitas mezclar código con configuración y reduces errores de rutas al construir imágenes.
- Preparas una estructura que escala: luego podrás añadir tests, scripts o nuevos servicios sin romper orden.

### 2) Crear archivo de variables de entorno

Crea `labs/02-docker-analitica/trabajo/stack-analitica/.env`:

```env
POSTGRES_USER=analytics
POSTGRES_PASSWORD=analytics
POSTGRES_DB=analytics
DATABASE_URL=postgresql+psycopg://analytics:analytics@db:5432/analytics
```

**Por que este paso es importante**

- Centralizas configuración para no hardcodear credenciales en el código.
- Permites cambiar entorno (local, staging, demo) tocando solo `.env`.
- El host `db` en `DATABASE_URL` es el nombre del servicio Docker Compose, no `localhost`; esto enseña cómo se resuelven servicios dentro de la red de Compose.

### 3) Crear contenido de la API (código + imagen)

Crea `labs/02-docker-analitica/trabajo/stack-analitica/api/requirements.txt`:

```text
fastapi==0.115.6
uvicorn[standard]==0.32.1
sqlalchemy==2.0.36
psycopg[binary]==3.2.3
```

Crea `labs/02-docker-analitica/trabajo/stack-analitica/api/app/main.py`:

```python
import os
from fastapi import FastAPI
from sqlalchemy import create_engine, text

app = FastAPI(title="analytics-stack")
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

Crea `labs/02-docker-analitica/trabajo/stack-analitica/api/Dockerfile`:

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY app ./app
ENV PYTHONUNBUFFERED=1
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

**Qué estamos haciendo aquí**

- Definir un servicio HTTP mínimo para validar salud y conexión real a DB.
- Empaquetar ese servicio en una imagen reproducible para ejecutarlo igual en cualquier entorno.

### 4) Crear contenido del ETL (código + imagen)

Crea `labs/02-docker-analitica/trabajo/stack-analitica/etl/requirements.txt`:

```text
pandas==2.2.2
sqlalchemy==2.0.36
psycopg[binary]==3.2.3
```

Crea `labs/02-docker-analitica/trabajo/stack-analitica/etl/etl.py`:

```python
import os
from sqlalchemy import create_engine, text
import pandas as pd

DATABASE_URL = os.environ["DATABASE_URL"]

def main() -> None:
    engine = create_engine(DATABASE_URL)
    df = pd.DataFrame({"metric": ["visitas", "ventas"], "value": [100, 7]})
    with engine.begin() as conn:
        conn.execute(text("CREATE TABLE IF NOT EXISTS metrics (metric TEXT, value INT)"))
        conn.execute(text("DELETE FROM metrics"))
        for _, row in df.iterrows():
            conn.execute(
                text("INSERT INTO metrics (metric, value) VALUES (:m, :v)"),
                {"m": row["metric"], "v": int(row["value"])},
            )
    print("ETL OK")
```

Crea `labs/02-docker-analitica/trabajo/stack-analitica/etl/Dockerfile`:

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY etl.py .
ENV PYTHONUNBUFFERED=1
CMD ["python", "-u", "etl.py"]
```

**Qué estamos haciendo aquí**

- Separar el procesamiento batch del servicio API.
- Asegurar que la lógica ETL puede ejecutarse de forma independiente y trazable.

### 5) Crear `docker-compose.yml` del stack

Crea `labs/02-docker-analitica/trabajo/stack-analitica/docker-compose.yml`:

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

**Qué estamos haciendo aquí**

- Definir los tres servicios como sistema único.
- Forzar orden de arranque saludable.
- Hacer ETL opcional (`profile`) para ejecutarlo solo cuando toque.

## Qué validas y qué debes ver

- La carpeta `stack-analitica` contiene `api/`, `etl/` y `docker-compose.yml`.
- El archivo `.env` existe con valores completos.
- API y ETL tienen `Dockerfile`, dependencias y código Python.

```bash
ls -R labs/02-docker-analitica/trabajo/stack-analitica
```

## Errores comunes

- Crear carpetas en la ruta equivocada (solución: rehacer desde la raíz).
- Escribir `DATABASE_URL` con `localhost` en lugar de `db`.

## Reto

Añade una carpeta `docs/` dentro de `stack-analitica` y crea un `README.md` con una línea explicando la arquitectura.

## Solución del reto

```bash
mkdir -p labs/02-docker-analitica/trabajo/stack-analitica/docs
echo "Stack: API + DB + ETL" > labs/02-docker-analitica/trabajo/stack-analitica/docs/README.md
```

## Navegacion del libro

- [Anterior](../03-compose-api-db-etl.md)
- [Siguiente](02-compose-up.md)
