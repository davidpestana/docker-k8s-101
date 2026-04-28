# Step 03 - Activar CronJob

## Objetivo del step

Configurar ejecución periódica del ETL y validar que Kubernetes crea jobs automáticamente según planificación.

## Fundamento del step

Este step no es solo ejecución de comandos: su objetivo es construir criterio técnico. Cada acción busca evitar errores frecuentes de entorno, de configuración o de integración entre servicios. Antes de avanzar, asegúrate de entender qué problema resuelve cada bloque.

## Ejecución guiada

### 1) Aplicar CronJob

```bash
kubectl apply -f labs/04-k8s-analitica/trabajo/k8s-batch/03-cronjob-etl.yaml
```

### 2) Comprobar estado del CronJob

```bash
kubectl -n analytics-lab get cronjobs
```

### 3) Observar jobs hijos

```bash
kubectl -n analytics-lab get jobs --watch
```

## Qué validas y qué debes ver

- CronJob activo con `SCHEDULE` correcto.
- Aparición de jobs hijos según planificación.
- Jobs finalizan correctamente.

## Errores comunes

- `schedule` mal definido (cron inválido).
- Solapamiento de ejecuciones por política de concurrencia.

## Reto

Dispara ejecución manual desde el CronJob sin esperar al próximo ciclo.

## Solución del reto

```bash
kubectl -n analytics-lab create job --from=cronjob/etl-cron etl-manual-$(date +%s)
kubectl -n analytics-lab get jobs
```

## Navegacion del libro

- [Anterior](02-ejecutar-job.md)
- [Siguiente](../../05-proyecto-final/README.md)
