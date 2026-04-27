# Orquestacion con docker-compose

## Qué vas a aprender

- Definir servicios multi-contenedor.
- Modelar dependencia básica entre servicios.
- Ejecutar stack con un solo comando.

## Referencia oficial

![Compose app diagram](https://docs.docker.com/compose/images/compose-app.webp)

Fuente: [Docker Docs - Docker Compose](https://docs.docker.com/compose/)

## Demo guiada

Crea `labs/01-docker-basico/trabajo/pg-demo/docker-compose.yml`:

```yaml
services:
  db:
    image: postgres:16
    environment:
      POSTGRES_USER: demo
      POSTGRES_PASSWORD: demo
      POSTGRES_DB: demo
  client:
    image: python:3.11-slim
    depends_on:
      - db
    command: >-
      bash -lc "pip install -q psycopg[binary]==3.1.18 &&
      python -u -c \"import psycopg; c=psycopg.connect('host=db user=demo password=demo dbname=demo'); print(c.execute('SELECT 1').fetchone())\""
```

Ejecuta:

```bash
cd labs/01-docker-basico/trabajo/pg-demo
docker compose up --abort-on-container-exit
```

Validación: el contenedor `client` imprime `(1,)`.

## Continuar

- [entornos-de-desarrollo-contenorizados.md](entornos-de-desarrollo-contenorizados.md)
## Navegacion del libro

- [Anterior](dockerfile-y-personalizacion-de-imagenes.md)
- [Siguiente](entornos-de-desarrollo-contenorizados.md)
