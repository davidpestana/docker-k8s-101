# Crear un contenedor

## Objetivo

Construir una imagen Python mínima y ejecutarla para validar entorno reproducible.

## Paso 1 - Crear estructura

```bash
mkdir -p labs/01-docker-basico/trabajo/mi-analitica/app
```

## Paso 2 - Crear `requirements.txt`

Ruta: `labs/01-docker-basico/trabajo/mi-analitica/requirements.txt`

```text
pandas==2.2.2
numpy==1.26.4
```

## Paso 3 - Crear `main.py`

Ruta: `labs/01-docker-basico/trabajo/mi-analitica/app/main.py`

```python
import pandas as pd
print(pd.DataFrame({"x": [1, 2], "y": [1, 4]}))
```

## Paso 4 - Crear `Dockerfile`

Ruta: `labs/01-docker-basico/trabajo/mi-analitica/Dockerfile`

```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY app ./app
CMD ["python", "-u", "app/main.py"]
```

## Demo breve

El profesor ejecuta:

```bash
cd labs/01-docker-basico/trabajo/mi-analitica
docker build -t mi-analitica:1.0 .
docker run --rm mi-analitica:1.0
```

## Siguiente capítulo

- [montando-volumenes-y-redes.md](montando-volumenes-y-redes.md)
