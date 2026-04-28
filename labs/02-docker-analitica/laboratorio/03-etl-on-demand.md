# Step 03 - ETL bajo demanda

## Objetivo del step

Ejecutar el ETL de forma controlada para validar procesamiento de datos sin depender del arranque continuo del stack.

## Contexto

En escenarios reales, el ETL suele ejecutarse por evento o por planificación. Aquí validas el modo manual y su salida.

## Fundamento del step

Este step no es solo ejecución de comandos: su objetivo es construir criterio técnico. Cada acción busca evitar errores frecuentes de entorno, de configuración o de integración entre servicios. Antes de avanzar, asegúrate de entender qué problema resuelve cada bloque.

## Ejecución guiada

### 1) Mantener DB disponible

Si no está levantada, inicia Compose en otra terminal.

### 2) Ejecutar ETL con profile

```bash
cd labs/02-docker-analitica/trabajo/stack-analitica
docker compose --profile etl run --rm etl
```

### 3) Verificar resultado funcional

```bash
curl -sS http://localhost:8000/db/ping
```

## Qué validas y qué debes ver

- El proceso ETL termina con código 0.
- Logs del ETL muestran ejecución completa (por ejemplo `ETL OK`).
- La API sigue operativa tras correr ETL.

## Errores comunes

- ETL falla por conexión: DB no lista o variables incorrectas.
- ETL no encuentra dependencias: revisar `requirements.txt` y Dockerfile del servicio `etl`.

## Reto

Ejecuta el ETL dos veces seguidas y compara si el comportamiento es idempotente (sin duplicar incoherencias).

## Solución del reto

```bash
docker compose --profile etl run --rm etl
docker compose --profile etl run --rm etl
```

Revisa logs y, si aplica, mejora el script ETL para limpiar/reemplazar antes de insertar.

## Navegacion del libro

- [Anterior](02-compose-up.md)
- [Siguiente](../../03-k8s-basico/README.md)
