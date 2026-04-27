# Step 03 - Aplicar overlay Kustomize

```bash
kubectl apply -k labs/03-k8s-basico/trabajo/k8s/overlays/local
kubectl -n analytics-lab get pods
```

Validación: al menos 2 pods de `analytics-api` en `Running`.

## Continuar

- [Ir al siguiente módulo](../../04-k8s-analitica/README.md)
