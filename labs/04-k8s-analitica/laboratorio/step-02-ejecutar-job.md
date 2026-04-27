# Step 02 - Ejecutar Job ETL

```bash
kubectl apply -f labs/04-k8s-analitica/trabajo/k8s-batch/02-job-etl-once.yaml
kubectl -n analytics-lab get jobs,pods
kubectl -n analytics-lab logs job/etl-once
```

## Siguiente

- [step-03-activar-cronjob.md](step-03-activar-cronjob.md)
## Navegacion del libro

- [Anterior](step-01-crear-configuracion.md)
- [Siguiente](step-03-activar-cronjob.md)
