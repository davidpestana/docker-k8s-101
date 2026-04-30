# Step 03 - Entorno dev con build context, volumen, puerto y auto-refresh

## Objetivo del step

Montar un entorno de desarrollo contenerizado donde puedas cambiar código en local y ver el cambio reflejado en el servicio sin reconstruir imagen en cada edición.

## Fundamento del step

Este step conecta cuatro conceptos clave de Docker en un caso real:

- **Build context**: define qué archivos ve Docker durante el build.
- **Volumen (bind mount)**: comparte código del host dentro del contenedor.
- **Puerto publicado**: hace accesible el servicio desde el navegador/cliente.
- **Auto-refresh**: acelera desarrollo al recargar el servicio al guardar archivos.

## Descripcion del laboratorio

Vas a crear una mini API en Python (Flask), construir su imagen y ejecutarla en modo desarrollo con recarga automática usando un bind mount del código.

## Ejecución guiada

### 1) Crear estructura de proyecto de desarrollo

```bash
mkdir -p labs/01-docker-basico/trabajo/dev-live/app
```

Crea `labs/01-docker-basico/trabajo/dev-live/requirements.txt`:

```text
flask==3.0.3
```

Crea `labs/01-docker-basico/trabajo/dev-live/app/main.py`:

```python
from flask import Flask, jsonify

app = Flask(__name__)

@app.get("/health")
def health():
    return jsonify({"status": "ok", "version": "v1"})

@app.get("/message")
def message():
    return jsonify({"message": "Hola desde Docker dev-live"})
```

### 2) Crear Dockerfile (con foco en build context)

Crea `labs/01-docker-basico/trabajo/dev-live/Dockerfile`:

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY app ./app

ENV FLASK_APP=app/main.py
ENV FLASK_RUN_HOST=0.0.0.0
ENV FLASK_RUN_PORT=8000

CMD ["flask", "run", "--debug"]
```

### 3) Build usando el contexto correcto

```bash
cd labs/01-docker-basico/trabajo/dev-live
docker build -t dev-live-api:1.0 .
```

Qué aprender aquí:

- El `.` final es el **build context**.
- Si haces build desde otra carpeta, `COPY app ./app` puede fallar o copiar archivos incorrectos.

### 4) Ejecutar contenedor con puerto y volumen

```bash
docker run --rm -it \
  --name dev-live-api \
  -p 8010:8000 \
  -v "$PWD/app:/app/app" \
  dev-live-api:1.0
```

Qué significa:

- `-p 8010:8000`: expone el puerto 8000 del contenedor en 8010 del host.
- `-v "$PWD/app:/app/app"`: código local montado dentro del contenedor.
- `--debug` en Flask activa recarga automática al detectar cambios.

### 5) Validar servicio en ejecución

En otra terminal:

```bash
curl -sS http://localhost:8010/health
curl -sS http://localhost:8010/message
```

Debes ver JSON válido con estado `ok`.

### 6) Probar auto-refresh modificando código

Sin detener el contenedor, edita `app/main.py` y cambia:

```python
return jsonify({"message": "Hola desde Docker dev-live"})
```

por:

```python
return jsonify({"message": "Mensaje actualizado en caliente"})
```

Vuelve a probar:

```bash
curl -sS http://localhost:8010/message
```

## Qué validas y qué debes ver

- Build exitoso con tag `dev-live-api:1.0`.
- Servicio accesible en `http://localhost:8010`.
- El cambio de código se refleja sin rebuild manual de imagen.
- En logs, Flask muestra recarga del proceso al guardar.

## Errores comunes

- Puerto ocupado: cambia `8010` por `8020` en `-p`.
- Volumen mal montado: revisar ruta absoluta de `$PWD/app`.
- Cambiaste archivo fuera de `app/`: no se refleja en el contenedor.

## Reto

Añade endpoint `GET /time` que devuelva hora UTC y confirma que se publica en caliente sin reiniciar contenedor.

## Solucion del reto

Añade en `app/main.py`:

```python
from datetime import datetime, timezone

@app.get("/time")
def get_time():
    return jsonify({"utc": datetime.now(timezone.utc).isoformat()})
```

Valida:

```bash
curl -sS http://localhost:8010/time
```

## Navegacion del libro

- [Anterior](02-interactividad-volumenes-puertos.md)
- [Siguiente](../../02-docker-analitica/README.md)
