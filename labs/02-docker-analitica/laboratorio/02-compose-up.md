# Step 02 - Levantar servicios con Compose

## Objetivo del step

Arrancar API y base de datos con Docker Compose y comprobar conectividad real entre servicios.

## Contexto

Compose simplifica el arranque coordinado. Si API y DB suben de forma correcta, ya tienes el núcleo del stack operativo.

## Preparación

Asegúrate de tener definidos:

- `docker-compose.yml`
- `.env`
- Dockerfiles y código en `api/` y `etl/`

## Fundamento del step

Este step no es solo ejecución de comandos: su objetivo es construir criterio técnico. Cada acción busca evitar errores frecuentes de entorno, de configuración o de integración entre servicios. Antes de avanzar, asegúrate de entender qué problema resuelve cada bloque.

## Ejecución guiada

### 1) Entrar al directorio del stack

```bash
cd labs/02-docker-analitica/trabajo/stack-analitica
```

### 2) Construir y levantar servicios principales

```bash
docker compose up --build
```

### 3) Validar API desde otra terminal

```bash
curl -sS http://localhost:8000/health
curl -sS http://localhost:8000/db/ping
```

## Qué validas y qué debes ver

- `health` devuelve estado OK.
- `db/ping` confirma conectividad con PostgreSQL.
- En logs, no debe haber reinicios infinitos de contenedores.

## Errores comunes

- Puerto ocupado en host (`8000`): cambia mapeo a `8001:8000`.
- API no conecta a DB: revisar `DATABASE_URL` y nombre de host `db`.

## Reto

Modifica el puerto expuesto de API a `8010` y valida `health` en el nuevo puerto.

## Solución del reto

En `docker-compose.yml`, cambia:

```yaml
ports:
  - "8010:8000"
```

Luego:

```bash
docker compose up --build
curl -sS http://localhost:8010/health
```

## Navegacion del libro

- [Anterior](01-estructura-stack.md)
- [Siguiente](03-etl-on-demand.md)
