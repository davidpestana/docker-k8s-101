# Job ETL de una ejecución

## Objetivo

Ejecutar carga batch una sola vez con `Job`.

## Archivo principal

`labs/04-k8s-analitica/trabajo/k8s-batch/02-job-etl-once.yaml`

## Demo breve

```bash
kubectl apply -f labs/04-k8s-analitica/trabajo/k8s-batch/02-job-etl-once.yaml
kubectl -n analytics-lab logs job/etl-once
```

## Continuar

- [cronjob-etl-programado.md](cronjob-etl-programado.md)
## Navegacion del libro

- [Anterior](README.md)
- [Siguiente](cronjob-etl-programado.md)
