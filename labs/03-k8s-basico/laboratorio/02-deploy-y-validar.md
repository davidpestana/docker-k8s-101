# Step 02 - Apply y validacion del despliegue

## Objetivo del step

Aplicar los manifiestos y comprobar que `Deployment` y `Service` funcionan como en el tutorial oficial, pero con flujo declarativo.

## Fundamento del step

El valor de este step es aprender a validar cada capa:

1. Kubernetes acepta los recursos.
2. El Deployment crea replicas sanas.
3. El Service enruta trafico a esos Pods.

## Ejecución guiada

### 1) Aplicar manifiestos

```bash
kubectl apply -f labs/03-k8s-basico/trabajo/k8s/manifests/
```

### 2) Ver estado de recursos

```bash
kubectl -n k8s-basics get deployments,pods,services
kubectl -n k8s-basics rollout status deployment/kubernetes-bootcamp
```

### 3) Validar Service y endpoints

```bash
kubectl -n k8s-basics get svc kubernetes-bootcamp
kubectl -n k8s-basics get endpoints kubernetes-bootcamp
```

### 4) Probar la app desde local con port-forward

```bash
kubectl -n k8s-basics port-forward service/kubernetes-bootcamp 8080:8080
```

En otra terminal:

```bash
curl -sS http://127.0.0.1:8080/
```

## Qué validas y qué debes ver

- `rollout status` finaliza correctamente.
- `endpoints` muestra IPs de Pods asociados.
- `curl` devuelve respuesta de la app (texto del bootcamp).

## Errores comunes

- `selector` del Service no coincide con labels del Deployment.
- Readiness probe mal configurada y Pods no pasan a `Ready`.
- Namespace incorrecto al ejecutar `kubectl`.

## Reto

Escala el Deployment a 4 replicas declarativamente y re-aplica manifiestos.

## Solución del reto

Edita `01-deployment.yaml` y cambia:

```yaml
replicas: 4
```

Luego:

```bash
kubectl apply -f labs/03-k8s-basico/trabajo/k8s/manifests/01-deployment.yaml
kubectl -n k8s-basics get pods -l app=kubernetes-bootcamp
```

## Navegacion del libro

- [Anterior](01-manifests-base.md)
- [Siguiente](03-kustomize-overlay.md)
