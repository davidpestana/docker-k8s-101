# Imágenes con kind y registry

## Objetivo

Versionar imagen y usarla en Kubernetes local y remoto.

## Demo breve

```bash
cd labs/02-docker-analitica/trabajo/stack-analitica
docker build -t analytics-api:0.1.0 ./api
kind load docker-image analytics-api:0.1.0 --name k8s-101
```

Opcional GHCR:

```bash
docker tag analytics-api:0.1.0 ghcr.io/TU_USUARIO/docker-k8s-101/analytics-api:0.1.0
docker push ghcr.io/TU_USUARIO/docker-k8s-101/analytics-api:0.1.0
```

## Continuar

- [kustomize-base-overlays.md](kustomize-base-overlays.md)
