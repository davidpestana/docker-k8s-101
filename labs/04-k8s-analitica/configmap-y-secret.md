# ConfigMap y Secret

## Objetivo

Separar configuración funcional y credenciales.

## Archivos

- `00-configmap-etl.yaml`
- `01-secret-etl-db.yaml`

## Demo breve

```bash
kubectl apply -f labs/04-k8s-analitica/trabajo/k8s-batch/00-configmap-etl.yaml
kubectl apply -f labs/04-k8s-analitica/trabajo/k8s-batch/01-secret-etl-db.yaml
kubectl -n analytics-lab get configmap,secret
```

# LABORATORIO

- [step-01-crear-configuracion.md](laboratorio/step-01-crear-configuracion.md)
