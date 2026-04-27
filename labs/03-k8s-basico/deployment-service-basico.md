# Deployment y Service básico

## Objetivo

Desplegar API y Postgres en `analytics-lab`.

## Archivos a crear

- `trabajo/k8s/manifests/00-namespace.yaml`
- `trabajo/k8s/manifests/01-postgres-secret.yaml`
- `trabajo/k8s/manifests/02-postgres.yaml`
- `trabajo/k8s/manifests/03-api-config.yaml`
- `trabajo/k8s/manifests/04-api.yaml`

## Demo breve

```bash
kubectl apply -f labs/03-k8s-basico/trabajo/k8s/manifests/
kubectl -n analytics-lab get pods,svc
```

## Continuar

- [imagenes-kind-y-registry.md](imagenes-kind-y-registry.md)
