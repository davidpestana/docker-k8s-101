# Pipeline build, push y deploy

## Objetivo

Conectar build local, registry y despliegue en Kubernetes.

## Demo breve

```bash
docker build -t proyecto-api:0.1.0 ./api
kind load docker-image proyecto-api:0.1.0 --name k8s-101
kubectl apply -k k8s/overlays/local
```

Opcional GHCR:

```bash
docker tag proyecto-api:0.1.0 ghcr.io/TU_USUARIO/docker-k8s-101/proyecto-api:0.1.0
docker push ghcr.io/TU_USUARIO/docker-k8s-101/proyecto-api:0.1.0
```

## Continuar

- [criterios-de-entrega.md](criterios-de-entrega.md)
