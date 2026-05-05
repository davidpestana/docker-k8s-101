# Kustomize: base y overlays

## Objetivo

Gestionar manifiestos Kubernetes de forma mantenible sin copiar/pegar YAML para cada entorno.

## Idea clave

`base` contiene lo comun.
`overlays` aplica diferencias concretas (replicas, labels, imagen, namespace, etc).

Esto permite:

- Menos duplicacion.
- Menos errores por drift entre entornos.
- Cambios mas trazables en Git.

## Estructura recomendada

- `trabajo/k8s/manifests/` -> manifiestos declarativos base del modulo.
- `trabajo/k8s/base/kustomization.yaml` -> agrega recursos base.
- `trabajo/k8s/overlays/local/kustomization.yaml` -> personaliza para local.

## Demo breve

```bash
kubectl apply -k labs/03-k8s-basico/trabajo/k8s/overlays/local
kubectl -n k8s-basics get deploy,svc,pods
```

## Qué debes entender antes del laboratorio

- `kubectl apply -f` aplica archivos tal cual.
- `kubectl apply -k` construye una vista final a partir de base + parches.
- Kustomize no requiere herramientas externas: ya va integrado en `kubectl`.

# LABORATORIO

- [01-manifests-base.md](laboratorio/01-manifests-base.md)

## Navegacion del libro

- [Anterior](02-imagenes-kind-y-registry.md)
- [Siguiente](laboratorio/01-manifests-base.md)
