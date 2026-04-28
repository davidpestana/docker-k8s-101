# Step 03 - Validación y entrega

## Objetivo del step

Consolidar evidencias técnicas de funcionamiento y cerrar entrega del proyecto final.

## Fundamento del step

Este step no es solo ejecución de comandos: su objetivo es construir criterio técnico. Cada acción busca evitar errores frecuentes de entorno, de configuración o de integración entre servicios. Antes de avanzar, asegúrate de entender qué problema resuelve cada bloque.

## Ejecución guiada

### 1) Validar estado global en cluster

```bash
kubectl get pods,svc -A
```

### 2) Recoger evidencias mínimas

- Respuesta de endpoint de salud.
- Logs de ejecución ETL/job.
- Estructura final del proyecto.

### 3) Checklist de entrega

- API operativa.
- Proceso ETL verificable.
- Manifiestos organizados en `base/overlays`.

## Qué validas y qué debes ver

- Servicios y pods activos en namespace esperado.
- Evidencias reproducibles por otro compañero.
- Coherencia entre documentación y comportamiento real.

## Errores comunes

- Entregar solo capturas sin comandos reproducibles.
- No incluir evidencia de batch/cron.

## Reto

Crea un breve informe `entrega.md` con: arquitectura, comandos clave y resultado de validación.

## Solución del reto

`entrega.md` debe incluir al menos:

1. Cómo levantar entorno.
2. Cómo desplegar en Kubernetes.
3. Qué comando demuestra que funciona.

## Navegacion del libro

- [Anterior](02-despliegue-final.md)
