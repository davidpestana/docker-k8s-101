# Step 02 - Ejecutar Job ETL

## Objetivo del step

Lanzar un Job batch en Kubernetes y validar su ciclo completo de ejecución.

## Fundamento del step

Este step no es solo ejecución de comandos: su objetivo es construir criterio técnico. Cada acción busca evitar errores frecuentes de entorno, de configuración o de integración entre servicios. Antes de avanzar, asegúrate de entender qué problema resuelve cada bloque.

## Ejecución guiada

### 1) Aplicar Job

```bash
kubectl apply -f labs/04-k8s-analitica/trabajo/k8s-batch/02-job-etl-once.yaml
```

### 2) Revisar estado

```bash
kubectl -n analytics-lab get jobs,pods
```

### 3) Revisar logs del Job

```bash
kubectl -n analytics-lab logs job/etl-once
```

## Qué validas y qué debes ver

- Job con `COMPLETIONS 1/1`.
- Pod del job finaliza en `Completed`.
- Logs muestran ejecución ETL completa.

## Errores comunes

- Imagen ETL no disponible en cluster (`ImagePullBackOff`).
- Secret/ConfigMap no montados correctamente.

## Reto

Ejecuta una segunda corrida manual con otro nombre y compara logs.

## Solución del reto

```bash
kubectl -n analytics-lab create job etl-once-manual --from=job/etl-once
kubectl -n analytics-lab logs job/etl-once-manual
```

## Navegacion del libro

- [Anterior](01-crear-configuracion.md)
- [Siguiente](03-activar-cronjob.md)
