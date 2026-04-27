# Step 02 - Deploy y validar

```bash
kubectl apply -f labs/03-k8s-basico/trabajo/k8s/manifests/
kubectl -n analytics-lab wait --for=condition=ready pod -l app=analytics-api --timeout=120s
kubectl -n analytics-lab port-forward svc/analytics-api 8080:80
```

Validar en otra terminal:

```bash
curl -sS http://127.0.0.1:8080/health
```

## Siguiente

- [step-03-kustomize-overlay.md](step-03-kustomize-overlay.md)
