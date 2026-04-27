# Step 03 - Activar CronJob

```bash
kubectl apply -f labs/04-k8s-analitica/trabajo/k8s-batch/03-cronjob-etl.yaml
kubectl -n analytics-lab get cronjobs
```

Validación: aparecen ejecuciones hijas en `kubectl -n analytics-lab get jobs`.

## Continuar

- [Ir al siguiente módulo](../../05-proyecto-final/README.md)
## Navegacion del libro

- [Anterior](step-02-ejecutar-job.md)
- [Siguiente](../../05-proyecto-final/README.md)
