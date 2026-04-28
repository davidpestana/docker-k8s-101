# Step 03 - Dockerfile, build y compose

## Fundamento del step

Este step no es solo ejecución de comandos: su objetivo es construir criterio técnico. Cada acción busca evitar errores frecuentes de entorno, de configuración o de integración entre servicios. Antes de avanzar, asegúrate de entender qué problema resuelve cada bloque.

## Descripcion del laboratorio

En este paso cierras el módulo construyendo una imagen propia y ejecutando un escenario multi-servicio con Compose para validar conectividad entre contenedores.

## Qué haces

1. Construyes una imagen desde Dockerfile.
2. Levantas un stack `Postgres + cliente Python` con Compose.
3. Compruebas que el cliente consulta correctamente la base de datos.

```bash
docker build -t custom-ubuntu-git .
cd labs/01-docker-basico/trabajo/pg-demo
docker compose up --abort-on-container-exit
```

## Qué validas y qué debes ver

- `docker build` finaliza con `Successfully tagged custom-ubuntu-git`.
- `docker compose up` crea servicios `db` y `client`.
- El contenedor `client` imprime `(1,)` al ejecutar `SELECT 1`.

## Reto

Modifica el `docker-compose.yml` para que la base de datos use el nombre `demo_db` y actualiza la conexión del cliente para seguir funcionando.

## Solucion del reto

```yaml
services:
  db:
    container_name: demo_db
```

En la conexión del cliente, mantiene `host=db` (nombre del servicio) para no romper la resolución DNS de Compose.

## Navegacion del libro

- [Anterior](02-interactividad-volumenes-puertos.md)
- [Siguiente](../../02-docker-analitica/README.md)
