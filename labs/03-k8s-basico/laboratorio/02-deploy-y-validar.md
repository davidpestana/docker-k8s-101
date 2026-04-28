# Step 02 - Deploy y validar

## Objetivo del step

Aplicar manifiestos en el cluster y verificar que API y DB quedan listas para tráfico.

## Fundamento del step

Este step no es solo ejecución de comandos: su objetivo es construir criterio técnico. Cada acción busca evitar errores frecuentes de entorno, de configuración o de integración entre servicios. Antes de avanzar, asegúrate de entender qué problema resuelve cada bloque.

## Ejecución guiada

### 1) Aplicar recursos

```bash
kubectl apply -f labs/03-k8s-basico/trabajo/k8s/manifests/
```

### 2) Esperar readiness

```bash
kubectl -n analytics-lab wait --for=condition=ready pod -l app=postgres --timeout=120s
kubectl -n analytics-lab wait --for=condition=ready pod -l app=analytics-api --timeout=120s
```

### 3) Probar API con port-forward

```bash
kubectl -n analytics-lab port-forward svc/analytics-api 8080:80
```

En otra terminal:

```bash
curl -sS http://127.0.0.1:8080/health
curl -sS http://127.0.0.1:8080/db/ping
```

## Qué validas y qué debes ver

- Pods en `Running` y `Ready`.
- Endpoints API respondiendo correctamente.
- `db/ping` confirma acceso a Postgres.

## Errores comunes

- `ImagePullBackOff`: imagen no cargada en kind o tag incorrecto.
- `CrashLoopBackOff`: revisar logs de deployment.

## Reto

Escala `analytics-api` a 3 réplicas y verifica que los 3 pods quedan listos.

## Solución del reto

```bash
kubectl -n analytics-lab scale deploy/analytics-api --replicas=3
kubectl -n analytics-lab get pods -l app=analytics-api
```

## Navegacion del libro

- [Anterior](01-manifests-base.md)
- [Siguiente](03-kustomize-overlay.md)
