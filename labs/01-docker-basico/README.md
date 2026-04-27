# Capitulo 2/6 - Docker basico (Modulo 2 del temario)

**Tiempo orientativo**: ~6h (bloques 2.1 a 2.6). Todo el trabajo del alumno vive bajo `labs/01-docker-basico/trabajo/` para no mezclar con el enunciado del curso.

## Objetivo

- Construir una **imagen** propia reproducible (Python + analitica ligera).
- Gestionar **ciclo de vida** del contenedor (`run`, logs, `stop`, `rm`).
- Usar **volumenes** para datos fuera del contenedor.
- Conectar **dos contenedores por red** (patron "app + base de datos" simplificado).
- Opcional temario: **Jupyter** y **PostgreSQL + cliente Python** como ejercicios cortos.

## Navegacion rapida

- [Anterior: Capitulo 1 - Prework](../00-prework/README.md)
- [Siguiente: Capitulo 3 - Docker para analitica](../02-docker-analitica/README.md)

---

## Donde crear archivos (estructura logica)

Desde la raiz del repo:

```bash
mkdir -p labs/01-docker-basico/trabajo/mi-analitica/app
mkdir -p labs/01-docker-basico/trabajo/mi-analitica/datos
```

Por que esta forma:

- `app/`: codigo Python **dentro** de una carpeta; evita mezclar codigo con datos y facilita `COPY` en el Dockerfile.
- `datos/`: volumen de practicas; representa CSV/parquet o salidas **persistentes**.
- `Dockerfile` y `requirements.txt` en la **raiz del mini-proyecto** (`mi-analitica/`): convencion estandar; `docker build` usa ese contexto.

```
labs/01-docker-basico/trabajo/mi-analitica/
  Dockerfile
  requirements.txt
  app/
    main.py
  datos/
    (ficheros generados en runtime)
```

---

## Bloque 2.1 - `requirements.txt` (~30 min)

**Crea** el archivo `labs/01-docker-basico/trabajo/mi-analitica/requirements.txt`:

```text
pandas==2.2.2
numpy==1.26.4
```

Por que versiones fijas: en analitica, **reproducibilidad** es clave; sin pin, dos builds en fechas distintas pueden diverger.

---

## Bloque 2.2 - Script de comprobacion `app/main.py` (~30 min)

**Crea** `labs/01-docker-basico/trabajo/mi-analitica/app/main.py`:

```python
import sys
import pandas as pd
import numpy as np


def main() -> None:
    df = pd.DataFrame({"x": np.arange(5), "y": np.arange(5) ** 2})
    print("Python:", sys.version.split()[0])
    print("pandas:", pd.__version__)
    print(df)


if __name__ == "__main__":
    main()
```

Por que: valida que el entorno contenedor tiene **numpy/pandas** coherentes antes de meter APIs o notebooks.

---

## Bloque 2.3 - Dockerfile linea a linea (~45 min)

**Crea** `labs/01-docker-basico/trabajo/mi-analitica/Dockerfile`:

```dockerfile
# Imagen base oficial y ligera con Python
FROM python:3.11-slim

# Directorio de trabajo dentro del contenedor
WORKDIR /app

# Dependencias primero: mejor cache de capas al cambiar solo codigo
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Codigo de aplicacion
COPY app ./app

# Comando por defecto del contenedor
CMD ["python", "-u", "app/main.py"]
```

Por que este orden:

1. `FROM`: define sistema base y version de Python.
2. `COPY requirements` + `RUN pip` antes del codigo: si solo cambias `main.py`, Docker **reutiliza** la capa de instalacion (build mas rapido).
3. `CMD`: proceso principal; `-u` fuerza salida sin buffer (logs utiles en `docker logs`).

**Build y run** (ejecuta dentro de `mi-analitica/`):

```bash
cd labs/01-docker-basico/trabajo/mi-analitica
docker build -t mi-analitica:1.0 .
docker run --rm mi-analitica:1.0
```

**Resultado esperado**: imprime versiones y un `DataFrame`.

**Si falla**:

- `permission denied` al copiar: revisa rutas y que estes en el directorio correcto.
- error de red en `pip`: reintenta; en Codespaces suele ser transitorio.

---

## Bloque 2.4 - Volumen para datos persistentes (~45 min)

Por que volumen: el sistema de archivos del contenedor es **efimero**; si borras el contenedor, pierdes salvo lo que este en un volumen o bind-mount.

**Crea** `labs/01-docker-basico/trabajo/mi-analitica/app/exportar.py`:

```python
from pathlib import Path
import pandas as pd

OUT = Path("/datos/salida.csv")


def main() -> None:
    OUT.parent.mkdir(parents=True, exist_ok=True)
    df = pd.DataFrame({"id": [1, 2], "valor": [10, 20]})
    df.to_csv(OUT, index=False)
    print("Escrito:", OUT.resolve())


if __name__ == "__main__":
    main()
```

**Sustituye temporalmente** el `CMD` del Dockerfile o ejecuta con override:

```dockerfile
# Opcional: segundo target; por simplicidad, ejecuta con docker run (abajo)
```

Build de nuevo:

```bash
docker build -t mi-analitica:1.0 .
docker run --rm -v "$PWD/datos:/datos" mi-analitica:1.0 python -u app/exportar.py
ls -la datos
```

**Resultado esperado**: aparece `datos/salida.csv` en tu maquina host (bind mount `./datos` -> `/datos`).

---

## Bloque 2.5 - Red entre contenedores (~45 min)

Por que: en analitica casi nunca tienes un solo proceso; patron tipico **API + base de datos** o **worker + cola**.

Crea una red bridge aislada:

```bash
docker network create analitica-net
```

Levanta un contenedor "servidor" que escucha en 9000 (imagen ligera con `python`):

```bash
docker run -d --name servidor --network analitica-net python:3.11-slim \
  python -m http.server 9000
```

Cliente en la misma red que llama por **nombre DNS** (`servidor`):

```bash
docker run --rm --network analitica-net curlimages/curl:8.5.0 \
  curl -sS http://servidor:9000/ | head
```

**Resultado esperado**: HTML listado del `http.server`.

Limpieza:

```bash
docker rm -f servidor
docker network rm analitica-net
```

---

## Bloque 2.6 - Jupyter en contenedor (~45 min, temario)

Sin Dockerfile propio todavia: usas imagen publica para ver el patron "herramienta de datos en contenedor".

```bash
docker run --rm -p 8888:8888 jupyter/scipy-notebook:notebook-7.2.2 \
  start-notebook.sh --NotebookApp.token='' --NotebookApp.password=''
```

Por que `--rm` y puerto: entorno desechable; `-p 8888:8888` publica la UI (en Codespaces, usa la pestaña "Ports" y abre 8888).

**Seguridad**: token vacio solo en laboratorio local; nunca en produccion.

Para parar: `Ctrl+C` en la terminal donde corre.

---

## Bloque 2.7 - PostgreSQL + cliente Python (~60 min, temario)

Crea carpeta `labs/01-docker-basico/trabajo/pg-demo/` y **crea** `docker-compose.yml`:

```yaml
services:
  db:
    image: postgres:16
    environment:
      POSTGRES_USER: demo
      POSTGRES_PASSWORD: demo
      POSTGRES_DB: demo
    ports:
      - "5432:5432"
    volumes:
      - pgdata:/var/lib/postgresql/data

  client:
    image: python:3.11-slim
    depends_on:
      - db
    environment:
      PGHOST: db
      PGUSER: demo
      PGPASSWORD: demo
      PGDATABASE: demo
    command: >-
      bash -lc "pip install -q psycopg[binary]==3.1.18 &&
      python -u -c \"import psycopg; conn=psycopg.connect('host=db user=demo password=demo dbname=demo'); print(conn.execute('SELECT 1').fetchone())\""
    networks:
      - default

volumes:
  pgdata:
```

Por que Compose aqui: dos servicios con **dependencia** y **red interna** sin escribir comandos `docker network` a mano cada vez.

```bash
cd labs/01-docker-basico/trabajo/pg-demo
docker compose up --abort-on-container-exit
```

**Resultado esperado**: el servicio `client` imprime `(1,)`.

---

## Entregables minimos (para autocorreccion)

- [ ] Imagen `mi-analitica:1.0` construida y ejecutando `main.py`.
- [ ] Archivo CSV generado via volumen en `mi-analitica/datos/`.
- [ ] Demo de red `analitica-net` completada o entendida.
- [ ] (Opcional) Jupyter accesible por puerto.
- [ ] (Opcional) `pg-demo` con `docker compose up` exitoso.

---

## Navegacion

- [Anterior: Capitulo 1 - Prework](../00-prework/README.md)
- [Siguiente: Capitulo 3 - Docker para analitica](../02-docker-analitica/README.md)
