# Imagenes con kind y registry

## Objetivo

Entender como hacer que un `Deployment` encuentre su imagen en local (kind) y en remoto (registry).

## Problema real

Cuando haces `kubectl apply`, Kubernetes no usa automaticamente las imagenes de tu Docker local.
Si el cluster es kind, necesitas cargar la imagen en los nodos de kind o publicarla en un registry.

## Flujo 1: entorno local con kind (rapido para clase)

```bash
docker build -t k8s-basics-web:1.0.0 labs/03-k8s-basico/trabajo/app
kind load docker-image k8s-basics-web:1.0.0 --name k8s-101
```

Luego, en tu manifiesto `Deployment`, usa exactamente ese tag:

```yaml
image: k8s-basics-web:1.0.0
imagePullPolicy: IfNotPresent
```

## Flujo 2: entorno remoto con registry (portable)

```bash
docker tag k8s-basics-web:1.0.0 ghcr.io/TU_USUARIO/docker-k8s-101/k8s-basics-web:1.0.0
docker push ghcr.io/TU_USUARIO/docker-k8s-101/k8s-basics-web:1.0.0
```

Y en `Deployment`:

```yaml
image: ghcr.io/TU_USUARIO/docker-k8s-101/k8s-basics-web:1.0.0
imagePullPolicy: Always
```

## Qué debes validar

- `kubectl describe pod ...` no muestra `ImagePullBackOff`.
- La app arranca y responde por el `Service`.
- El tag de imagen que usas en YAML coincide con el cargado/publicado.

## Continuar

- [03-kustomize-base-overlays.md](03-kustomize-base-overlays.md)

## Navegacion del libro

- [Anterior](01-deployment-service-basico.md)
- [Siguiente](03-kustomize-base-overlays.md)
