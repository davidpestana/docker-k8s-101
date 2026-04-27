# Kustomize base y overlays

## Objetivo

Separar configuración común y variaciones por entorno.

## Archivos a crear

- `trabajo/k8s/base/kustomization.yaml`
- `trabajo/k8s/overlays/local/kustomization.yaml`

## Demo breve

```bash
kubectl apply -k labs/03-k8s-basico/trabajo/k8s/overlays/local
kubectl -n analytics-lab get pods
```

# LABORATORIO

- [step-01-manifests-base.md](laboratorio/step-01-manifests-base.md)
## Navegacion del libro

- [Anterior](imagenes-kind-y-registry.md)
- [Siguiente](laboratorio/step-01-manifests-base.md)
