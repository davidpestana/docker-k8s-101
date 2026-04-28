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

### 3) Crear esqueleto de archivos de servicios

```bash
touch labs/02-docker-analitica/trabajo/stack-analitica/api/Dockerfile
touch labs/02-docker-analitica/trabajo/stack-analitica/api/requirements.txt
touch labs/02-docker-analitica/trabajo/stack-analitica/api/app/main.py
touch labs/02-docker-analitica/trabajo/stack-analitica/etl/Dockerfile
touch labs/02-docker-analitica/trabajo/stack-analitica/etl/requirements.txt
touch labs/02-docker-analitica/trabajo/stack-analitica/etl/etl.py
touch labs/02-docker-analitica/trabajo/stack-analitica/docker-compose.yml
```

**Por que este paso es importante**

- Defines el contrato técnico del stack antes de implementar: ya sabes qué piezas existen y cómo se conectan.
- Permite trabajar por iteraciones pequeñas: primero estructura, después contenido y validación.
- Facilita revisar el proyecto con otros (compañero o profesor) porque la arquitectura queda visible desde el árbol de archivos.

## Qué validas y qué debes ver

- La carpeta `stack-analitica` contiene `api/`, `etl/` y `docker-compose.yml`.
- El archivo `.env` existe con valores completos.

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
