# Montando volúmenes y redes

## Objetivo

Persistir datos fuera del contenedor y conectar contenedores por red.

## Volúmenes

Crea archivo `labs/01-docker-basico/trabajo/mi-analitica/app/exportar.py`:

```python
from pathlib import Path
import pandas as pd
Path('/datos').mkdir(exist_ok=True)
pd.DataFrame({'id':[1,2]}).to_csv('/datos/salida.csv', index=False)
print('ok')
```

Ejecuta:

```bash
cd labs/01-docker-basico/trabajo/mi-analitica
docker run --rm -v "$PWD/datos:/datos" mi-analitica:1.0 python -u app/exportar.py
ls -la datos
```

## Redes

```bash
docker network create analitica-net
docker run -d --name servidor --network analitica-net python:3.11-slim python -m http.server 9000
docker run --rm --network analitica-net curlimages/curl:8.5.0 curl -sS http://servidor:9000/ | head
docker rm -f servidor
docker network rm analitica-net
```

## Demo breve

El profesor muestra por qué `servidor` funciona como DNS interno de Docker.

## Siguiente capítulo

- [docker-compose-postgres-python.md](docker-compose-postgres-python.md)
